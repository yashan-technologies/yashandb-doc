Creating a PDB requires operating on the CDB root in OPEN state. PDBs can be created through the [CREATE PLUGGABLE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PLUGGABLE DATABASE) statement or the [yasboot pdb add](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot pdb) command.

## Preparations

- Ensure the current database is a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE).

- The CDB root executing the operation is in OPEN state. In primary-standby deployment, operations must be performed on the primary CDB root.

- The following information has been planned for the PDB:

    - Data file storage path (PDB_DATA). You can choose to directly use the default path as needed (no additional preparation required) or customize and plan a path (create the path + configure the path conversion rule when creating the PDB).

      
        If you need to plan a custom data file path for a newly created PDB, you must first complete the creation of the corresponding path (and permission configuration) before creating the PDB, and complete the path conversion when creating the PDB.
        
        |Deployment Form  |Default PDB_DATA  |Requirements for Custom PDB_DATA  |
        | ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
        | Standalone Deployment              | $YASDB_DATA/containers/{pdb_name} | Must be a local path, and the database installation user must have read-write permissions     |
        | YAC/Distributed Cluster Deployment | +DG0/containers/{pdb_name}        |  Must be a [YFS](../../../Database Administration/Storage Management/YFS Management/00YFS Management) path    |
        
        > **Note**:
        >
        > If you need to use the originally planned custom PDB_DATA when retrying after the PDB creation fails, you must also ensure that the target path is empty before retrying.
        
        

    - PDB local users. If creation is needed, plan the username and password. Defaults to no creation.

    - Attributes of the USERS tablespace (DEFAULT).

    - Whether to enable archive mode.

    - PDB syntax mode. Defaults to yashan mode. If MySQL syntax compatibility is needed, it is recommended to specify mysql mode.

- If using the yasboot command to create PDB, prepare the PDB configuration file (pdb_add.toml) according to the above planning.
    
    When directly deploying YashanDB as a CDB, this file will be automatically generated in the $YASDB_HOME directory.

    > **Note**:
    >
    > - When using the yasboot command to create PDB, the syntax mode cannot be specified, and only yashan mode PDBs can be created.
    >
    > - If deployed as a non-CDB (without specifying --enable-pluggable-database) but subsequently converted to a CDB through configuration modification, such as upgrading from v23.4 or lower versions to v23.5 or above, then yasboot cannot be used for PDB-related operations. 

## Operational Steps

### Standalone Deployment

::: tabs
== Method 1: SQL Statement

1. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute the CREATE PLUGGABLE DATABASE statement to create a PDB.

   ```sql
   CREATE PLUGGABLE DATABASE pdb1
   FILE_NAME_CONVERT=('?/containers/PDB$SEED/','?/pdb1/')
   ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1_password
   DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M
   ARCHIVELOG;
   ```

3. View PDB information.

   ```sql
   -- Method 1: View through yasql command
   show pdbs

                  CON_ID CON_NAME                                                         STATUS
   --------------------- ---------------------------------------------------------------- -----------------
                       1 PDB$SEED                                                         CLOSED
                       2 PDB1                                                             OPEN

   -- Method 2: View through V$CONTAINERS view
   SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN

   -- Method 3: View through V$PDBS view
   SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/db-1-1/containers/PDB1/                YASHAN
   ```

== Method 2: yasboot Tool

1. Log in to the database installation server using the installation user.


2. Edit the PDB configuration file according to the planning.

   ```shell
   $ cd $YASDB_HOME
   vi pdb_add.toml
   ```

   File content example:

   ```text
   file-name-convert="'?/containers/PDB$SEED/dbfiles','?/pdb1/dbfiles','?/containers/PDB$SEED/local_fs','?/pdb1/local_fs'"
   user="sys_pdb1"
   password="sys_pdb1_password"
   default-table-space-file="userspdb1"
   default-table-space-size="128M"
   isarchive="true"
   ```

3. Execute the following command to create the PDB.

   ```shell
   $ yasboot pdb add -c yashandb --pdb pdb1 --pdb-config $YASDB_HOME/pdb_add.toml
   ```
   Prompt "task completed, status: SUCCESS" indicates successful creation.

4. View PDB information.

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

:::

###  YAC/Distributed Cluster Deployment

::: tabs
== Method 1: SQL Statement


In a YAC/Distributed Cluster Deployment CDB, the process for adding PDBs through *ycsctl* is as follows, and is performed while the CDB is running.

1. Log in to any database installation server as the installation user and execute the ycsctl add pdb command to add a PDB resource to the YCS.

2. Log in to the CDB root and execute the CREATE PLUGGABLE DATABASE statement to add a new PDB, and the PDB name must be consistent.


Specific operational steps examples are as follows:

1. Log in to the database installation server using the installation user.


2. Execute the following command to add a PDB to the CDB cluster.

   ```shell
   $ ycsctl add pdb -db yashandb -pdb pdb1
   ```

3. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


4. Execute the CREATE PLUGGABLE DATABASE statement to create the PDB.

   The PDB name must be consistent with the name specified in step 2. If a file conversion path needs to be specified, the YFS path must be used.

   ```sql
   CREATE PLUGGABLE DATABASE pdb1
   FILE_NAME_CONVERT=('+DG0/containers/PDB\$SEED/dbfiles', '+DG1/PDB2/dbfiles')
   ADMIN USER sys_pdb1 IDENTIFIED BY sys_pdb1_password
   DEFAULT TABLESPACE DATAFILE 'userspdb1' size 128M
   ARCHIVELOG;
   ```

5. View PDB information.

   ```sql
   -- Method 1: View through yasql command
   show pdbs

                  CON_ID CON_NAME                                                         STATUS
   --------------------- ---------------------------------------------------------------- -----------------
                       1 PDB$SEED                                                         CLOSED
                       2 PDB1                                                             OPEN

   -- Method 2: View through GV$CONTAINERS view
   SELECT INST_ID,NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM GV$CONTAINERS WHERE INST_ID = 1;

   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/ce-1-1                                 YASHAN
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/ce-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/ce-1-1/containers/PDB1/                YASHAN

   -- Method 3: View through GV$PDBS view
   SELECT INST_ID,NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM GV$PDBS WHERE INST_ID = 1;
   NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
   ---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
   PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/ce-1-1/containers/PDB$SEED/            YASHAN
   PDB1                                                             PDB               OPEN              /data/yashan/yasdb_data/ce-1-1/containers/PDB1/                YASHAN
   ```

== Method 2: yasboot Tool

1. Log in to the database installation server using the installation user.


2. Edit the PDB configuration file according to the planning.

   ```shell
   $ cd $YASDB_HOME
   vi pdb_add.toml
   ```

   File content example:

   ```text
   file-name-convert="'+DG0/containers/PDB$SEED/dbfiles','+DG1/pdb1/dbfiles'"
   user="sys_pdb1"
   password="sys_pdb1_password"
   default-table-space-file="userspdb1"
   default-table-space-size="128M"
   isarchive="true"
   ```

3. Execute the following command to create the PDB.

   ```shell
   $ yasboot pdb add -c yashandb --pdb pdb1 --pdb-config $YASDB_HOME/pdb_add.toml
   ```
   Prompt "task completed, status: SUCCESS" indicates successful creation.

4. View PDB information.

   ```shell
   $ yasboot pdb status -c yashandb --pdb pdb1
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hostid   | nodeid | node_type | pdb_name | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address   | data_path                      |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | host0001 | 1-1:1  | cdb       | pdb1     | 31165 | open            | normal     | primary  | -           | 192.168.1.2:1688 | /data/yashan/yasdb_data/ce-1-1 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
   | host0002 | 1-2:2  | cdb       | pdb1     | -     | closed          | -          | -        | -           | 192.168.1.3:1688 | /data/yashan/yasdb_data/ce-1-2 |
   +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
   ```

:::
