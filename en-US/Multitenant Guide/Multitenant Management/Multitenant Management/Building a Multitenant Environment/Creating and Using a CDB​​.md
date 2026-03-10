If the `--enable-pluggable-database` command option is specified when installing YashanDB, the initial database after installation will directly be a CDB and can be used immediately.

## Create a CDB

If `--enable-pluggable-database` was not specified during installation or some configurations of the initial database do not meet business requirements, refer to this document to recreate the CDB.

### Prerequisites

- The currently installed YashanDB version is v23.5 or above.

- YashanDB is deployed in Standalone (Primary-Standby) Deployment, YAC Deployment (cannot be Primary-Standby Cluster Deployment), or Distributed Cluster Deployment.

- The DROP DATABASE statement has been executed to [delete the current database](../../../../All Manuals/Database Administration/Instance Management/Database Dropping).

### Operational Steps

1. Log in to the database installation server using the installation user.


2. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    Confirm according to the echo information whether the current database deployment form (node_type field) meets requirements: 
    
    - `node_type=cdb` indicates the current environment is already a CDB, and can continue to the next step.
    
    - `node_type=db` or `node_type = ce & database_role != standby` indicates the current environment is a Standalone/YAC (non-primary-standby)/Distributed Cluster Deployment non-CDB, and can continue to the next step.
    
    - Other scenarios indicate the current environment does not meet requirements and cannot continue operations.

3. If `instance_status=close`, the database needs to be [started](../../../../All Manuals/Database Administration/Instance Management/Instance Startup and Shutdown) to NOMOUNT state.

    ```shell
    $ yasboot cluster start -c yashandb -m nomount
    ```

4. Execute the following command to check the configuration of the ENABLE_PLUGGABLE_DATABASE parameter.

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
    If it is FALSE, execute the following command to adjust the configuration, and restart the database to make the configuration effective.

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

5. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

6. Execute the CREATE DATABASE statement to create a CDB.

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

## CDB Startup and Shutdown

In Standalone Deployment, CDB startup and shutdown is equivalent to [instance startup and shutdown](../../../../All Manuals/Database Administration/Instance Management/Instance Startup and Shutdown), which will simultaneously start or stop the CDB root and all PDBs.

In YAC/Distributed Cluster Deployment, CDB startup and shutdown is equivalent to [cluster startup and shutdown](../../../../All Manuals/Database Administration/Cluster Management/Cluster Startup and Shutdown), which will simultaneously start or stop the CDB root and all PDBs.

After logging into the YashanDB installation server as the installation user, execute the following command to view the running status (`instance_status` field).

```shell
$ yasboot cluster status -c yashandb -d
```

## View Container Information

When creating a CDB, the system will automatically initialize two built-in containers:



- CDB root: The current instance will be converted to the CDB root, with the default name `CDB$ROOT`. The CDB root itself is a fully functional database instance with independent data files and transaction systems, but its core responsibility is to manage the entire CDB's metadata, global users, resource plans, and PDB lifecycle. All container management operations (such as creating and deleting PDBs) must be executed in the CDB root.  

- PDB seed: The system will automatically create a read-only PDB named PDB$SEED as the standard template for new PDBs, which is default in closed state. Its configuration is defined by the SEED clause, and undefined or undefinable parts of the configuration will default to inheriting from the CDB root, such as character set. Subsequent PDB creation (CREATE PLUGGABLE DATABASE) is all based on cloning PDB$SEED, including system tablespace structure, initial users, permission configuration, etc.


In subsequent daily usage, container information and status can be viewed through [V$CONTAINERS](../../../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$CONTAINERS)/[GV$CONTAINERS](../../../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/GV$CONTAINERS) and [V$PDBS](../../../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$PDBS)/[GV$PDBS](../../../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/GV$PDBS).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Query the status of all containers (including CDB$ROOT)
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$CONTAINERS;

NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
CDB$ROOT                                                         ROOT              OPEN              /data/yashan/yasdb_data/db-1-1                                 YASHAN
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN

-- Query the status of all PDBs
SELECT NAME,TYPE,STATUS,HOME,COMPAT_MODE FROM V$PDBS;
NAME                                                             TYPE              STATUS            HOME                                                             COMPAT_MODE
---------------------------------------------------------------- ----------------- ----------------- -------------------------------------------------------------- -----------------
PDB$SEED                                                         SEED              CLOSED            /data/yashan/yasdb_data/db-1-1/containers/PDB$SEED/            YASHAN
```
