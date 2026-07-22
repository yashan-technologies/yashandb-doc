创建PDB需在OPEN状态的根容器上操作，可以通过[CREATE PLUGGABLE DATABASE](../../../开发手册/SQL参考手册/SQL语句/CREATE PLUGGABLE DATABASE.md)语句或[yasboot pdb add](../../../工具手册/yasboot/yasboot命令介绍/yasboot pdb.md)命令创建。

## 准备工作

- 确保当前数据库为容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）。

- 执行操作的根容器处于OPEN状态。在主备部署中，需在主根容器上操作。

- 已为PDB规划以下信息：

    - 数据文件存放路径（PDB_DATA），可按需选择直接使用默认路径（无额外准备工作）或自定义规划路径（创建路径 + 创建PDB时配置路径转换规则）。

      
        如需为新建PDB规划自定义数据文件路径，在创建PDB前必须先完成对应路径的创建（以及权限配置），并在创建PDB时完成路径转换。
        
        | 部署形态| 默认PDB_DATA| 自定义PDB_DATA要求|
        | ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
        | 单机部署                           | $YASDB_DATA/containers/{pdb_name} | 必须为本地路径，且数据库安装用户必须具备读写权限             |
        | 共享集群/分布式集群部署            | +DG0/containers/{pdb_name}        | 必须为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径 |
        
        > **Note**:
        >
        > 若PDB创建失败后再次重试时仍需使用原计划的自定义PDB_DATA，重试前还需确保目标路径为空。
        
        

    - PDB本地用户，如需创建则需规划用户名和密码，默认不创建。

    - USERS表空间（DEFAULT）的属性。

    - 是否开启归档模式。

    - PDB的语法模式，默认为yashan模式，若需适配MySQL的语法兼容，建议指定为mysql模式。

- 如需使用yasboot命令创建PDB，还需根据上述规划准备PDB配置文件（pdb_add.toml）。
    
    直接将YashanDB部署为容器数据库时，在$YASDB_HOME目录下会自动生成该文件。

    > **Note**:
    >
    > 若部署时为非容器数据库（未指定--enable-pluggable-database）但后续修改配置将其转换为容器数据库，例如从v23.4或更低版本升级至v23.5及以上场景，则无法使用yasboot进行PDB相关操作。

## 操作步骤

### 单机部署

#### 方式1：使用SQL语句

1. 以DBA用户连接并登录根容器。
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. 执行CREATE PLUGGABLE DATABASE语句，创建PDB。

   ```sql
   CREATE PLUGGABLE DATABASE pdb1
   FILE_NAME_CONVERT=('?/containers/PDB$SEED/','?/pdb1/')
   ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1_password
   DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M
   ARCHIVELOG;
   ```

3. 查看PDB信息。

   ```sql
   -- 方式1：通过yasql命令查看
   show pdbs

                  CON_ID CON_NAME                                                         STATUS
   --------------------- ---------------------------------------------------------------- -----------------
                       1 PDB$SEED                                                         CLOSED
                       2 PDB1                                                             OPEN

   -- 方式2：通过V$CONTAINERS视图查看
   SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN

   -- 方式3：通过V$PDBS视图查看
   SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN
   ```

#### 方式2：使用yasboot工具 

1. 以安装用户登录数据库安装服务器。


2. 根据规划编辑PDB配置文件。

   ```shell
   $ cd $YASDB_HOME
   vi pdb_add.toml
   ```

   文件内容示例：

   ```text
   file-name-convert="'?/containers/PDB$SEED/dbfiles','?/pdb1/dbfiles','?/containers/PDB$SEED/local_fs','?/pdb1/local_fs'"
   user="sys_pdb1"
   password="sys_pdb1_password"
   default-table-space-file="userspdb1"
   default-table-space-size="128M"
   isarchive="true"
   ```

3. 执行以下命令，创建PDB。

   ```shell
   $ yasboot pdb add -c yashandb --pdb pdb1 --pdb-config $YASDB_HOME/pdb_add.toml
   ```
   提示`task completed, status: SUCCESS`表示创建成功。

4. 查看PDB信息。

   ```shell
   $ yasboot pdb status -c yashandb --pdb pdb1
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hostid   | nodeid | node_type | pdb_name | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address    | data_path                      |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | host0001 | 1-1:1  | cdb       | pdb1     | 30526 | open            | open       | primary  | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+-------------------+--------------------------------+
   | host0002 | 1-2:2  | cdb       | pdb1     | 12745 | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+-------------------+--------------------------------+
   ```

### 共享集群/分布式集群部署 

#### 方式1：使用SQL语句


在容器数据库集群中，通过ycsctl工具新增PDB的完整流程如下，在数据库运行时操作即可。

1. 以安装用户登录任一数据库安装服务器，执行ycsctl add pdb命令向YCS添加PDB资源。

2. 登录根容器，执行CREATE PLUGGABLE DATABASE语句新增PDB，且PDB名称需一致。


具体的操作步骤示例如下：

1. 以安装用户登录数据库安装服务器。


2. 执行[ycsctl add pdb](../../../工具手册/ycsctl/ycsctl使用指导/数据库资源管理命令.md#addpdb)命令，向容器数据库集群中添加PDB。

   ```shell
   $ ycsctl add pdb -db yashandb -pdb pdb1
   ```

   - 通过可选参数-policy可以指定PDB的启动策略，即是否跟随根容器启动。

   - 通过可选参数-startoption可以指定PDB的默认启动阶段选项，若在后续启动PDB时不单独指定启动选项，则直接复用此处指定的值作为默认值。

3. 以DBA用户连接并登录根容器。
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


4. 执行CREATE PLUGGABLE DATABASE语句，创建PDB。

   PDB名称必须与步骤2中指定的名称一致，如需指定文件转换路径需使用YFS路径。

   ```sql
   CREATE PLUGGABLE DATABASE pdb1
   FILE_NAME_CONVERT=('+DG0/containers/PDB\$SEED/dbfiles', '+DG1/PDB2/dbfiles')
   ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1_password
   DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M
   ARCHIVELOG;
   ```

5. 查看PDB信息。

   ```sql
   -- 方式1：通过yasql命令查看
   show pdbs

                  CON_ID CON_NAME                                                         STATUS
   --------------------- ---------------------------------------------------------------- -----------------
                       1 PDB$SEED                                                         CLOSED
                       2 PDB1                                                             OPEN

   -- 方式2：通过GV$CONTAINERS视图查看
   SELECT INST_ID,NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM GV$CONTAINERS WHERE INST_ID = 1;

   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/ce-1-1                                 YASHAN
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/ce-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/ce-1-1/containers/PDB1/                YASHAN

   -- 方式3：通过GV$PDBS视图查看
   SELECT INST_ID,NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM GV$PDBS WHERE INST_ID = 1;
   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/ce-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/ce-1-1/containers/PDB1/                YASHAN
   ```

#### 方式2：使用yasboot工具 

1. 以安装用户登录数据库安装服务器。


2. 根据规划编辑PDB配置文件。

   ```shell
   $ cd $YASDB_HOME
   vi pdb_add.toml
   ```

   文件内容示例：

   ```text
   file-name-convert="'+DG0/containers/PDB$SEED/dbfiles','+DG1/pdb1/dbfiles'"
   user="sys_pdb1"
   password="sys_pdb1_password"
   default-table-space-file="userspdb1"
   default-table-space-size="128M"
   isarchive="true"
   ```

3. 执行以下命令，创建PDB。

   ```shell
   $ yasboot pdb add -c yashandb --pdb pdb1 --pdb-config $YASDB_HOME/pdb_add.toml
   ```
   提示`task completed, status: SUCCESS`表示创建成功。

4. 查看PDB信息。

   ```shell
   $ yasboot pdb status -c yashandb --pdb pdb1
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hostid   | nodeid | node_type | pdb_name | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address   | data_path                      |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | host0001 | 1-1:1  | cdb       | pdb1     | 31165 | open            | normal     | primary  | -           | 192.168.1.2:1688 | /data/yashan/yasdb_data/ce-1-1 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
   | host0002 | 1-2:2  | cdb       | pdb1     | 31179 | open            | normal     | primary  | -           | 192.168.1.3:1688 | /data/yashan/yasdb_data/ce-1-2 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
   ```
