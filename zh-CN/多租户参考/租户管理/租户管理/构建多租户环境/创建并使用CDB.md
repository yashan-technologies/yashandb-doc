若在安装YashanDB时指定了--enable-pluggable-database命令选项，安装完成后初始数据库将直接是容器数据库，可直接使用。

## 创建CDB

若安装时未指定--enable-pluggable-database或初始数据库部分配置不满足业务要求，可参考本文重新创建CDB。

### 前提条件

- 当前安装的YashanDB版本为v23.5或以上。

- YashanDB采用单机（主备）部署、共享集群部署（不能为主备集群部署）或分布式集群部署。

- 已执行DROP DATABASE语句[删除当前数据库](../../../../全部手册/数据库管理/实例管理/数据库删除)。

### 操作步骤

1. 以安装用户登录数据库安装服务器。


2. 执行以下命令，查看数据库状态。

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    根据回显信息确认当前数据库的部署形态（node_type字段）是否符合要求：
    
    - `node_type=cdb`表示当前环境已是容器数据库，可继续执行下一步。
    
    - `node_type=db`或`node_type = ce & database_role != standby`表示当前环境为单机部署/共享集群部署（非主备）/分布式集群部署的非容器数据库，可继续执行下一步。
    
    - 其他情形则表示当前环境不符合要求，无法继续操作。

3. 若instance_status=close，需[启动数据库](../../../../全部手册/数据库管理/实例管理/实例启停)至NOMOUNT状态。

    ```shell
    $ yasboot cluster start -c yashandb -m nomount
    ```

4. 执行以下命令，检查ENABLE_PLUGGABLE_DATABASE参数的配置。

    ```shell
    $ yasboot cluster config show -c yashandb -q enable_pluggable_database
    +------------------------------------+
    | node   | ENABLE_PLUGGABLE_DATABASE |
    +------------------------------------+
    | db-1-1 | FALSE                     |
    +--------+---------------------------+
    | db-1-2 | FALSE                     |
    +--------+---------------------------+
    ```
    若为FALSE，需执行以下命令调整配置，并重启数据库使配置生效。

    ```shell
    $ yasboot cluster config set -c yashandb -k enable_pluggable_database -v true
    $ yasboot cluster restart -c yashandb -m nomount
    $ yasboot cluster config show -c yashandb -q enable_pluggable_database
    +------------------------------------+
    | node   | ENABLE_PLUGGABLE_DATABASE |
    +------------------------------------+
    | db-1-1 | TRUE                      |
    +--------+---------------------------+
    | db-1-2 | TRUE                      |
    +--------+---------------------------+
    ```

5. 以sys用户登录数据库。
    
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
    

6. 执行CREATE DATABASE语句，创建CDB。

    ```sql
    CREATE DATABASE yashancdb
        ENABLE PLUGGABLE DATABASE
            SEED
                SYSTEM TABLESPACE DATAFILE 'system' size 128M
                SYSAUX TABLESPACE DATAFILE 'sysaux' size 128M
        SYSTEM TABLESPACE DATAFILE 'system' size 500M
        SYSAUX TABLESPACE DATAFILE 'sysaux' size 500M
        DEFAULT TABLESPACE DATAFILE 'usersroot' size 500M
        ARCHIVELOG;
    ```

## CDB启停

在单机部署中，CDB启停等同于[实例启停](../../../../全部手册/数据库管理/实例管理/实例启停)，会同时启停根容器和所有PDB。

在共享集群/分布式集群部署中，CDB启停等同于[集群启停](../../../../全部手册/数据库管理/集群管理/集群启停)，会同时启停根容器和所有PDB。

以安装用户登录YashanDB安装服务器后，执行以下命令可以查看运行状态（instance_status字段）。

```shell
$ yasboot cluster status -c yashandb -d
```

## 查看容器信息

创建CDB时，系统会自动初始化两个内置容器：



- 根容器（CDB root）：当前实例将转换为CDB Root，默认名称为`CDB$ROOT`。根容器本身是一个功能完整的数据库实例，拥有独立的数据文件和事务系统，但其核心职责是管理整个CDB的元数据、全局用户、资源计划及PDB生命周期。所有的容器管理操作（例如创建、删除PDB）必须在根容器中执行。  

- 种子容器（PDB seed）：系统会自动创建一个名称为PDB$SEED的只读PDB，作为创建PDB的标准模板， 默认处于关闭状态。其配置由SEED子句定义，未定义或无法定义的部分配置会默认继承至根容器，例如字符集。后续创建PDB（CREATE PLUGGABLE DATABASE）时均会通过克隆PDB$SEED生成，包括系统表空间结构、初始用户、权限配置等。  


在后续日常使用中，可以通过[V$CONTAINERS](../../../../全部手册/参考手册/系统视图/动态视图/V$CONTAINERS)/[GV$CONTAINERS](../../../../全部手册/参考手册/系统视图/动态视图/GV$CONTAINERS)、[V$PDBS](../../../../全部手册/参考手册/系统视图/动态视图/V$PDBS)/[GV$PDBS](../../../../全部手册/参考手册/系统视图/动态视图/GV$PDBS)查看容器信息和状态。

示例（单机/共享集群/分布式集群部署）

```sql
-- 查询所有容器的状态（包括CDB$ROOT)
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN

-- 查询所有PDB的状态
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
```
