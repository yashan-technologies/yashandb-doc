本文将介绍容器数据库中的全局资源管理，如需使用PDB本地资源管理请查阅[配置资源管理](./配置资源管理.md)。

## 环境准备

在使用**CPU**资源管理前，需要先确保服务器已开启CGroups。



1. 以安装用户登录数据库安装服务器。


2. 执行以下命令确认是否已开启CGroups。

    ```shell
    $ mount | grep -E "cgroup|cgroup2"
    # 或
    $ sudo mount | grep -E "cgroup|cgroup2"
    ```

    若该命令无回显信息，表示未开启CGroups。此时，需先为每台服务器[开启CGroups](../../安装和升级/安装部署/安装前准备/配置安装环境.md#CGroups)才能执行后续操作。

3. 执行以下命令确认CPU和IO功能是否可用。

    ```shell
    $ cat /sys/fs/cgroup/cgroup.subtree_control
    ```

    - 若回显信息中包含`cpu`和`io`，表示功能可用，无需进行其他操作。

    - 若回显信息中不包含`cpu`和`io`，需执行以下命令启用。

        ```shell
        $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
        $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
        ```



## 启用资源管理

### 步骤1：（可选）配置资源上限基数值

请根据是否开启IOPS资源、内存资源以及相应的资源限额规划，按需修改对应的配置参数为合适值。

在单机主备部署或主备集群部署中，下列参数均可在主、备根容器中独立配置，再根据资源计划可生成差异化的资源上限值。

|  配置参数| 对应的资源| 描述| 默认值|
| ----------------------------- | ----------------| -------------------------- | ---------------------- |
| [MAX_BPS](../../参考手册/配置参数.md#max_bps) | IOPS资源  | 指定容器数据库中PDB允许使用的每秒读写字节数的上限基数。开启CDB全局资源管理时，该值将用于计算目标PDB可用的每秒读写字节数上限，计算公式为MAX_BPS * UTILIZATION_LIMIT | 默认为0，即不对相应资源进行限制  |
| [MAX_IOPS](../../参考手册/配置参数.md#max_iops) | IOPS资源  | 指定容器数据库中PDB每秒IO操作次数的上限基数。开启CDB全局资源管理时，该值将用于计算目标PDB每秒IO操作次数的上限，计算公式为MAX_IOPS * UTILIZATION_LIMIT | 默认为0，即不对相应资源进行限制  |
| [CDB_MAX_MEMORY_SIZE](../../参考手册/配置参数.md#cdb_max_memory_size) | 内存资源  | 指定容器数据库中所有容器（根容器、所有PDB）可用物理内存总和的上限。开启CDB全局资源管理时，该值将用于计算目标PDB可用物理内存的上/下限，计算公式为CDB_MAX_MEMORY_SIZE * 对应百分比 | 默认为0，即可用服务器总内存的80% |

1. 以DBA用户连接并登录根容器。
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. 查看当前配置。

    ```sql
    SHOW PARAMETER MAX_BPS

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    MAX_BPS                                                          0

    SHOW PARAMETER MAX_IOPS

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    MAX_IOPS                                                         0

    SHOW PARAMETER CDB_MAX_MEMORY_SIZE

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    CDB_MAX_MEMORY_SIZE                                              0
    ```

3. 按需修改对应参数的值。

    ```sql
    ALTER SYSTEM SET MAX_IOPS=2147483647;
    ```

    >**Note**:
    >
    > 在单机主备部署或主备集群部署中，还可按需在备根容器上重复执行上述操作配置差异化的参数值。

### 步骤2：创建CDB资源计划指令

1. 以sys用户登录根容器。
    
    ```shell
    # 本地登录时可以使用操作系统认证方式登录
    $ yasql / as sysdba

    # 使用密码认证方式登录
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. 调用[DBMS_RESOURCE_MANAGER](../../开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN创建CDB资源计划。

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
        'RES_CDB_PLAN',
        'RESOURCE PLAN FOR CDB MANAGEMENT');
    ```

### 步骤3：为PDB关联资源计划指令

#### 方式1：配置专属的CDB资源计划指令

调用[DBMS_RESOURCE_MANAGER](../../开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN_DIRECTIVE为指定PDB创建专属的CDB资源计划指令。

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'PDB1',
    COMMENT => 'RESOURCE PLAN DIRECTIVE FOR PLUGGABLE DATABASE PDB1',
    SHARES => 25,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 100,
    MEMORY_LIMIT => 50,
    MEMORY_MIN => 20);
```

#### 方式2：为PDB关联CDB资源模板指令

1. 调用[DBMS_RESOURCE_MANAGER](../../开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PROFILE_DIRECTIVE创建CDB资源模板指令。

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PROFILE_DIRECTIVE(
        PLAN => 'RES_CDB_PLAN',
        PROFILE => 'RES_CDB_PROFILE',
        COMMENT => 'RESOURCE PROFILE DIRECTIVE',
        SHARES => 10,
        UTILIZATION_LIMIT => 30,
        PARALLEL_SERVER_LIMIT => 50,
        MEMORY_LIMIT => 25,
        MEMORY_MIN => 10);
    ```

2. 调用[DBMS_RESOURCE_MANAGER](../../开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).SET_PDB_PROFILE设置PDB与CDB资源模板指令的映射关系。

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
        PLUGGABLE_DATABASE => 'PDB1','PDB2',
        PROFILE => 'RES_CDB_PROFILE'); 
    ```

### 步骤4：激活并启用资源管理

1. 以安装用户登录数据库安装服务器。


2. 通过yasboot工具修改RESOURCE_MANAGER_PLAN参数，以激活资源计划RES_CDB_PLAN。

    ```shell
    # -r参数表示修改失败后回滚集群参数
    $ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v RES_CDB_PLAN -d -r

    # 可通过如下命令查看RESOURCE_MANAGER_PLAN参数是否已生效于所有节点
    $ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
    ```

    > **Note**:
    >
    > `RESOURCE_MANAGER_PLAN`参数只允许使用`yasboot cluster config set`命令修改。


3. 以DBA用户连接并登录根容器。
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


4. 检查RESOURCE_MANAGER_PLAN参数配置是否生效。

    ```sql
    show parameter RESOURCE_MANAGER_PLAN;

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    RESOURCE_MANAGER_PLAN                                            RES_CDB_PLAN                                                      ALL
    ```

通过上述示例成功开启CDB全局资源管理功能后，还可以按需在直连PDB进行[本地资源管理配置](./配置资源管理.md)。

> **Warn**:
>
> 在启用资源管理的情况下，重启服务器可能会导致`/sys/fs/cgroup/cgroup.subtree_control`文件中的CPU和IO配置丢失，从而导致数据库无法启动（此时运行日志run.log中会记录`cgroup directory %s is not existed, please create cgroup directory first`报错信息）。遇此问题时，请执行以下命令恢复CPU和IO功能后再启动数据库。
>
> ```shell
> $ sudo sh -c 'find /sys/fs/cgroup -name "cgroup.procs" -exec cat {} \; 2>/dev/null | sort -u | while read pid; do [ -n "$pid" ] && echo $pid 2>/dev/null; done > /sys/fs/cgroup/cgroup.procs'
> $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> ```

## 查看CDB资源配置

通过[DBA_CDB_RSRC_PLANS](../../参考手册/系统视图/DBA视图/DBA_CDB_RSRC_PLANS.md)视图可以查询所有CDB资源计划。

```sql
select plan,comments from dba_cdb_rsrc_plans;
PLAN                                                             COMMENTS
---------------------------------------------------------------- -----------------------------------------------------------
DEFAULT_CDB_PLAN                                                 DEFAULT RESOURCE PLAN FOR CDB$ROOT
```

通过[DBA_CDB_RSRC_PLAN_DIRECTIVES](../../参考手册/系统视图/DBA视图/DBA_CDB_RSRC_PLAN_DIRECTIVES.md)视图可以查看所有CDB资源计划指令。

```sql
select plan, pluggable_database, shares, utilization_limit, memory_min, memory_limit from dba_cdb_rsrc_plan_directives;
PLAN                                                             PLUGGABLE_DATABASE                                                    SHARES UTILIZATION_LIMIT  MEMORY_MIN MEMORY_LIMIT
---------------------------------------------------------------- ---------------------------------------------------------------- ----------- ----------------- ----------- ------------
DEFAULT_CDB_PLAN                                                                                                                            1               100           1          100
RES_CDB_PLAN                                                                                                                                1               100           1          100
RES_CDB_PLAN                                                     PDB1                                                                      25                50          20           50
```

## 查看PDB资源使用情况

通过[V$RSRC_PDB](../../参考手册/系统视图/动态视图/V$RSRC_PDB.md)可以查看所有PDB的资源使用情况。

```sql
select pdb_name, cpu_wait_time, cpu_waits, consumed_cpu_time, sga_bytes, buffer_cache_bytes, shared_pool_bytes from v$rsrc_pdb;
```
