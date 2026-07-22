Based on actual application scenarios, PDB deletion may involve the following scenarios:

- Scenario 1: Completely delete PDBs that are no longer needed.

- Scenario 2: Temporarily delete PDBs as a preceding operation for PDB operations, such as deleting standby PDBs but retaining metadata before reconstructing standby databases, or deleting PDBs but retaining metadata before recovering PDB data.

## Scenario 1: Completely Delete PDB

If it is confirmed that a PDB is no longer needed, it can be completely deleted to avoid resource waste. If it is primary-standby deployment, this scenario means completely cleaning up the primary and standby databases of a PDB.

> **Warn**:
>
> Deleting a PDB will simultaneously delete PDB-related persistent files and cannot be rolled back. **Please operate with caution**.

### Prerequisites

- The CDB root is in OPEN state. If it is primary-standby deployment, only the primary CDB root is required to be in OPEN state, and the corresponding operations must be executed by logging into the primary CDB root.

- The target PDB must be in the closed state.

###  Operational Steps

::: tabs
== Using *yasboot*



1. Log in to the database installation server using the installation user.


2. Obtain target PDB information, including status, name, etc.

    ```shell
    $ yasboot cluster status -c yashandb -d
    +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | nodeid | node_type | pdb_name       | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address    | data_path                        |
    +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 1-1:1  | cdb       | cdb$root       | 19053 | open            | open       | primary  | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1 |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    |          |        |           | pdb1           | 12366 | open            | open       | primary  | -           |                   |                                  |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    |          |        |           | pdb2           | 29317 | open            | open       | primary  | -           |                   |                                  |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    +----------+--------+-----------+----------------+-------+-----------------+------------+----------+-------------+-------------------+----------------------------------+
    | host0002 | 1-2:2  | cdb       | cdb$root       | 7755  | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2 |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    |          |        |           | pdb1           | 12745 | open            | open       | standby  | unknown     |                   |                                  |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    |          |        |           | pdb2           | 31570 | open            | open       | standby  | 1-1:1       |                   |                                  |
    +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
    +----------+--------+-----------+----------------+-------+-----------------+------------+----------+-------------+-------------------+----------------------------------+
    ```
    

3. If the target PDB is running, it needs to be closed before executing subsequent operations. If it is primary-standby deployment, only the primary PDB needs to be closed.

    ```shell
    $ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb2
    ```

4. Execute the yasboot pdb drop command to delete the target PDB, and enter the confirmation information according to the prompts.

    ```shell
    $ yasboot pdb drop -c yashandb --pdb pdb2
    Are you sure you want to drop pdb pdb2 ?[yes/no]:yes
    ```

5. Confirm the deletion result.

    ```shell
    $ yasboot cluster status -c yashandb -d
    +---------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | nodeid | node_type | pdb_name | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address    | data_path                      |
    +---------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 1-1:1  | cdb       | cdb$root | 19053 | open            | open       | primary  | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1 |
    +          +        +           +----------+-------+-----------------+------------+----------+-------------+                   +                                +
    |          |        |           | pdb1     | 12508 | open            | open       | primary  | -           |                   |                                |
    +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+-------------------+--------------------------------+
    | host0002 | 1-2:2  | cdb       | cdb$root | 7755  | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2 |
    +          +        +           +----------+-------+-----------------+------------+----------+-------------+                   +                                +
    |          |        |           | pdb1     | 12745 | open            | open       | standby  | 1-1:1       |                   |                                |
    +----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+-------------------+--------------------------------+
    ```

== Execute SQL statements in the CDB root

1. Connect and log in to the CDB root (must be the primary CDB root in primary-standby deployment) as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Obtain target PDB information, including status, name, etc.

    ```sql
    show pdbs

                   CON_ID CON_NAME                                                         STATUS
    --------------------- ---------------------------------------------------------------- -----------------
                        1 PDB$SEED                                                         CLOSED
                        2 PDB1                                                             OPEN
                        3 PDB2                                                             CLOSED
    ```

3. If the target PDB is running, it needs to be [closed](./​Starting and Stopping a PDB​​.md#close) before executing subsequent operations.

    ```sql
    ALTER PLUGGABLE DATABASE pdb2 CLOSE;
    ```

4. Execute the DROP PLUGGABLE DATABASE statement to delete the target PDB:

    If it is confirmed that the target PDB is not needed, no persistent files need to be retained. Specify the INCLUDING ARCHIVELOG keyword to delete archive log files as well.

    ```sql
    DROP PLUGGABLE DATABASE pdb2 INCLUDING ARCHIVELOG;
    ```

5. Confirm the deletion result.

    ```sql
    show pdbs

                   CON_ID CON_NAME                                                         STATUS
    --------------------- ---------------------------------------------------------------- -----------------
                        1 PDB$SEED                                                         CLOSED
                        2 PDB1                                                             OPEN
    ```

6. If it is YAC/Distributed Cluster Deployment, the ycsctl remove pdb command must also be executed to remove the PDB information from the cluster.

    ```shell
    SQL> exit
    $ ycsctl remove pdb -db yashandb -pdb pdb2
    ```
:::

## Scenario 2: Temporarily Delete PDB as a Preceding Operation for PDB Operations

As a preceding operation for PDB operations, scenarios where PDB needs to be temporarily deleted mainly include the following:

- As a prerequisite operation for [PDB recovery](../../Backup and Recovery/Backup and Recovery in CDB/00Backup and Recovery in CDB): Specify the KEEP METADATA keyword to retain PDB metadata, and archive log files are retained by default. If PITR recovery is performed, the KEEP LOGFILE keyword must also be additionally specified to retain the target PDB's redo log files.

- As a prerequisite operation for rebuilding PDB standby database: Simultaneously specify the KEEP METADATA keyword and INCLUDING ARCHIVELOG keyword to retain only the PDB metadata.

### Prerequisites

- The CDB root where the target PDB is located is required to be in OPEN state. For example, if the standby PDB1 to be deleted is located on the primary CDB root, then only the primary CDB root is required to be in OPEN state.

- Target PDB status requirements:

    - When connecting to CDB root for execution: The target PDB must be in closed state.

    - When directly connecting to target PDB for execution: It must be in NOMOUNT stage.

###  Operational Steps

1. (Optional) If it is primary-standby deployment, first confirm the location of the target standby database for the target PDB.

    1. ) Log in to the database installation server using the installation user.


    2. ) Obtain target PDB information, including status, name, primary-standby role, and the node where it is located.

        ```shell
        $ yasboot cluster status -c yashandb -d -p sys_password
        +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
        | hostid   | nodeid | node_type | pdb_name       | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address    | data_path                        |
        +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
        | host0001 | 1-1:1  | cdb       | cdb$root       | 19053 | open            | open       | primary  | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1 |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        |          |        |           | pdb1           | 12366 | open            | open       | primary  | -           |                   |                                  |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        |          |        |           | pdb2           | 29317 | open            | open       | primary  | -           |                   |                                  |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        +----------+--------+-----------+----------------+-------+-----------------+------------+----------+-------------+-------------------+----------------------------------+
        | host0002 | 1-2:2  | cdb       | cdb$root       | 7755  | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2 |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        |          |        |           | pdb1           | 12745 | open            | open       | standby  | unknown     |                   |                                  |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        |          |        |           | pdb2           | 31570 | open            | open       | standby  | 1-1:1       |                   |                                  |
        +          +        +           +----------------+-------+-----------------+------------+----------+-------------+                   +                                  +
        +----------+--------+-----------+----------------+-------+-----------------+------------+----------+-------------+-------------------+----------------------------------+
        ```

        This chapter will use the deletion of the standby database of PDB2 as an example for subsequent introduction. From the above information, it can be seen that it is located on the standby CDB root (192.168.1.3:1688).
    
    3. ) If the target PDB is running, it needs to be adjusted to an appropriate running state before executing subsequent operations.

        ```shell
        # When connecting to the CDB root to execute deletion operations, PDB needs to be closed
        $ yasboot pdb stop -c yashandb -n 1-2 --pdb pdb2 -p sys_password

        # When directly connecting to PDB to execute deletion operations, the PDB must be in NOMOUNT stage
        $ yasboot pdb restart -c yashandb -n 1-2 --pdb pdb2 -p sys_password -m nomount
        ```

2. Delete the PDB but retain its metadata.

    ::: tabs
    == Execute SQL statements in the CDB root

1. ) Connect and log in to the the CDB root where the target PDB is located as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.3:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. ) Execute the DROP PLUGGABLE DATABASE statement and specify the corresponding keywords as needed to retain the required files:
    ```sql
    -- Retain metadata + archive log files + redo log files
    DROP PLUGGABLE DATABASE pdb2 KEEP LOGFILE KEEP METADATA;

    -- Retain metadata + archive log files
    DROP PLUGGABLE DATABASE pdb2 KEEP METADATA;

    -- Retain metadata
    DROP PLUGGABLE DATABASE pdb2 KEEP METADATA INCLUDING ARCHIVELOG;
    ```

    == Execute SQL statements in the target PDB

1. ) Log in to the target PDB as the sys user.
    
    ```shell
    $ yasql sys/********@192.168.1.3:1688/pdb1

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. ) Execute the DROP DATABASE statement and specify the corresponding keywords as needed to retain the required files:
    ```sql
    -- Retain metadata + archive log files + redo log files
    DROP DATABASE KEEP LOGFILE KEEP METADATA;

    -- Retain metadata + archive log files
    DROP DATABASE KEEP METADATA;

    -- Retain metadata
    DROP DATABASE KEEP METADATA INCLUDING ARCHIVELOG;
    ```
    :::
