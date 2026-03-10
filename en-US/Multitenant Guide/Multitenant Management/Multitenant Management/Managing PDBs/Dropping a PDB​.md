> **Warn**:
>
> Deleting a PDB will simultaneously delete PDB-related persistent files and cannot be rolled back. **Please operate with caution**.

The following methods can be used to delete unnecessary PDBs:

| Methods   | Description                 |
| -------------------------------- | -------------------------------------------- |
| Execute [DROP PLUGGABLE DATABASE](../../../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/DROP PLUGGABLE DATABASE) statement in the CDB root | Can delete a PDB or all PDBs at a time, with optional selection to retain the target PDB's redo log files, archive log files, or PDB metadata as needed. |
| [*yasboot*](../../../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot pdb)                                                | Can delete PDBs individually or in batch at a time, but this operation completely destroys the PDB and cannot selectively retain partial persistent files.   |

## Prerequisites

- The CDB root executing the operation is in OPEN state. In primary-standby deployment, operations must be performed on the primary CDB root.

- The target PDB is in closed state.

## Connect to Root Container and Execute SQL Statement for Deletion

1. Connect and log in to the CDB root as a DBA user.
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

    If the target PDB is running, it needs to be [closed](./​Starting and Stopping a PDB​​.html#close) before executing subsequent operations.

3. Execute the DROP PLUGGABLE DATABASE statement to delete the target PDB, and partial persistent files can be retained according to operational scenarios:

    - As a prerequisite operation for PDB recovery: Specify the KEEP METADATA keyword to retain PDB metadata, and archive log files are retained by default. If PITR recovery is performed, the KEEP LOGFILE keyword must also be additionally specified to retain the target PDB's redo log files.

    ```sql
    DROP PLUGGABLE  DATABASE pdb2 KEEP METADATA;
    -- or
    DROP PLUGGABLE  DATABASE pdb2 KEEP LOGFILE KEEP METADATA;
    ```

    - As a prerequisite operation for rebuilding PDB standby database: Simultaneously specify the KEEP METADATA keyword and INCLUDING ARCHIVELOG keyword to retain only the PDB metadata.

    ```sql
    DROP PLUGGABLE DATABASE pdb2 KEEP METADATA INCLUDING ARCHIVELOG;
    ```

    - Target PDB is confirmed to be unnecessary: No persistent files need to be retained, specify the INCLUDING ARCHIVELOG keyword to delete archive log files as well.

    ```sql
    DROP PLUGGABLE DATABASE pdb2 INCLUDING ARCHIVELOG;
    ```

4. Confirm the deletion result.

    ```sql
    show pdbs

                   CON_ID CON_NAME                                                         STATUS
    --------------------- ---------------------------------------------------------------- -----------------
                        1 PDB$SEED                                                         CLOSED
                        2 PDB1                                                             OPEN
    ```

5. If it is YAC/Distributed Cluster Deployment, the ycsctl remove pdb command must also be executed to remove the PDB information from the cluster.

    ```shell
    SQL> exit
    $ ycsctl remove pdb -db yashandb -pdb pdb2
    ```

##  Using *yasboot*



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
    

    If the target PDB is running, it needs to be closed before executing subsequent operations. If it is primary-standby deployment, only the primary PDB needs to be closed.

    ```shell
    $ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb2
    ```

3. Execute the yasboot pdb drop command to delete the target PDB, and enter the confirmation information according to the prompts.

    ```shell
    $ yasboot pdb drop -c yashandb --pdb pdb2
    Are you sure you want to drop pdb pdb2 ?[yes/no]:yes
    ```

    Prompt "task completed, status: SUCCESS" indicates successful execution.

4. Confirm the deletion result.

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

