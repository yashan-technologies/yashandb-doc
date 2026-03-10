Before performing the scaling operations, please carefully read the relevant precautions:

- It is recommended to [back up](../../Database Administration/Backup and Recovery/00Backup and Recovery) the database before performing scaling operations to ensure that there is a backup set available for recovery.

- If new servers are needed for expansion, please refer to the [pre-installation preparation](../Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that the environment/configuration of the new servers meets the requirements.

- Scaling operations cannot be performed concurrently with the following operations:
    
    - [Standalone primary/standby scaling](Scalability of Standalone Deployment/00Scalability of Standalone Deployment): switchover, tablespace, and other data file addition/deletion/RESIZE operations.

    - [YAC scaling](Scalability of YAC Deployment/00Scalability of YAC Deployment): add_instance_clauses in ALTER DATABASE, cluster switchover, tablespace and other data file addition/deletion/RESIZE, redo file addition/deletion, creation/running of YStream servers, distributed transactions, backup/recovery, and joining other instances to YAC (that is, starting from NOMOUNT phase to MOUNT/OPEN phase) and other operations.

    - [ISC distributed cluster scaling](Scalability of ISC Distributed Cluster Deployment/00Scalability of ISC Distributed Cluster Deployment): switchover, scaling DN group during which DDL will be blocked and the following *yasboot* commands will be intercepted:

        ```shell
        $ yasboot cluster restart
        $ yasboot group restart
        $ yasboot node restart
        $ yasboot group add/remove
        $ yasboot node add/remove
        $ yasboot dataspace redistribute
        ```

- During the scaling process, do not forcibly restart/stop any nodes in the current database environment using *yasboot*, as this may cause scaling failures and prevent successful execution of node remove --clean or group remove --clean commands.

- Before ISC distributed cluster scaling DN groups ([Scalability of ISC Distributed Cluster Deployment/DN Groups Scaling](Scalability of ISC Distributed Cluster Deployment/DN Groups Scaling)), please also note:

    - Ensure that all dependent tables of OUTLINE exist. You can confirm all SQL statements corresponding to OUTLINE by querying the DBA_OUTLINES view and then further verify that the tables involved in the statements exist. If a dependent table of an OUTLINE does not exist, it must be deleted before performing the expansion operation; otherwise, an error will occur.

    - Ensure that all dependent DIRECTORIES of external tables exist. You can confirm that all DIRECTORIES corresponding to DIRECTORY_NAME of external tables exist by querying the DBA_EXTERNAL_LOCATIONS view. If a dependent DIRECTORY of an external table does not exist, it must be deleted before performing the expansion operation; otherwise, an error will occur.

- After expansion, the final configuration must not exceed the corresponding [specifications](../../Product Overview/Specifications/Physical Specifications), such as the maximum number of standby databases (backup clusters), maximum number of nodes/node groups, and the [number of instances](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE.html#maxinstances) in YACs, etc.
