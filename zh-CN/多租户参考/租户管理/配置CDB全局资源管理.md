本文将介绍容器数据库中的全局资源管理，如需使用PDB本地资源管理请查阅[配置资源管理](../../全部手册/数据库管理/资源管理/配置资源管理)。

## 启用资源管理

### （可选）配置资源上限基数值

请根据是否开启IOPS资源、内存资源以及相应的资源限额规划，按需修改对应的配置参数为合适值。

|  配置参数| 对应的资源| 描述| 默认值|
| ----------------------------- | ----------------| -------------------------- | ---------------------- |
| [MAX_BPS](../../全部手册/参考手册/配置参数.html#max_bps) | IOPS资源  | 指定容器数据库中PDB允许使用的每秒读写字节数的上限基数。开启CDB全局资源管理时，该值将用于计算目标PDB可用的每秒读写字节数上限，计算公式为MAX_BPS * UTILIZATION_LIMIT | 默认为0，即不对相应资源进行限制  |
| [MAX_IOPS](../../全部手册/参考手册/配置参数.html#max_iops) | IOPS资源  | 指定容器数据库中PDB每秒IO操作次数的上限基数。开启CDB全局资源管理时，该值将用于计算目标PDB每秒IO操作次数的上限，计算公式为MAX_IOPS * UTILIZATION_LIMIT | 默认为0，即不对相应资源进行限制  |
| [CDB_MAX_MEMORY_SIZE](../../全部手册/参考手册/配置参数.html#cdb_max_memory_size) | 内存资源  | 指定容器数据库中所有容器（根容器、所有PDB）可用物理内存总和的上限。开启CDB全局资源管理时，该值将用于计算目标PDB可用物理内存的上/下限，计算公式为CDB_MAX_MEMORY_SIZE * 对应百分比 | 默认为0，即可用服务器总内存的80% |

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

### 创建CDB资源计划指令

调用[DBMS_RESOURCE_MANAGER](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN创建CDB资源计划。

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
        'RES_CDB_PLAN',
        'RESOURCE PLAN FOR CDB MANAGEMENT');
    ```

### 为PDB关联资源计划指令

#### 方式1：配置专属的CDB资源计划指令

调用[DBMS_RESOURCE_MANAGER](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN_DIRECTIVE为指定PDB创建专属的CDB资源计划指令。

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

1. 调用[DBMS_RESOURCE_MANAGER](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).CREATE_CDB_PROFILE_DIRECTIVE创建CDB资源模板指令。

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

2. 调用[DBMS_RESOURCE_MANAGER](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_RESOURCE_MANAGER).SET_PDB_PROFILE设置PDB与CDB资源模板指令的映射关系。

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
        PLUGGABLE_DATABASE => 'PDB1','PDB2',
        PROFILE => 'RES_CDB_PROFILE'); 
    ```

### 激活并启用资源管理

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

通过上述示例成功开启CDB全局资源管理功能后，还可以按需在直连PDB进行[本地资源管理配置](../../全部手册/数据库管理/资源管理/配置资源管理)。

## 查看CDB资源配置

通过[DBA_CDB_RSRC_PLANS](../../全部手册/参考手册/系统视图/DBA视图/DBA_CDB_RSRC_PLANS)视图可以查询所有CDB资源计划。

```sql
SELECT plan,comments FROM DBA_CDB_RSRC_PLANS;
PLAN                                                             COMMENTS
---------------------------------------------------------------- -----------------------------------------------------------
DEFAULT_CDB_PLAN                                                 DEFAULT RESOURCE PLAN FOR CDB$ROOT
```

通过[DBA_CDB_RSRC_PLAN_DIRECTIVES](../../全部手册/参考手册/系统视图/DBA视图/DBA_CDB_RSRC_PLAN_DIRECTIVES)视图可以查看所有CDB资源计划指令。

```sql
SELECT plan, pluggable_database, shares, utilization_limit, memory_min, memory_limit FROM DBA_CDB_RSRC_PLAN_DIRECTIVES;
PLAN                                                             PLUGGABLE_DATABASE                                                    SHARES UTILIZATION_LIMIT  MEMORY_MIN MEMORY_LIMIT
---------------------------------------------------------------- ---------------------------------------------------------------- ----------- ----------------- ----------- ------------
DEFAULT_CDB_PLAN                                                                                                                            1               100           1          100
RES_CDB_PLAN                                                                                                                                1               100           1          100
RES_CDB_PLAN                                                     PDB1                                                                      25                50          20           50
```

## 查看PDB资源使用情况

通过[V$RSRC_PDB](../../全部手册/参考手册/系统视图/动态视图/V$RSRC_PDB)可以查看所有PDB的资源使用情况。

```sql
SELECT pdb_name, cpu_wait_time, cpu_waits, consumed_cpu_time, sga_bytes, buffer_cache_bytes, shared_pool_bytes FROM V$RSRC_PDB;
```
