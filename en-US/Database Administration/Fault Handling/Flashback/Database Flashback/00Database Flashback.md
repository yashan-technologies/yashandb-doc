Database flashback allows rolling back data to a historical point-in-time by reverting changes to database objects, without traditional backup restoration. It functions similarly to Point-in-Time Recovery (PITR) but is more efficient and streamlined, meeting stricter RPO and RTO requirements.

The SQL statement is FLASHBACK DATABASE TO ……, detailed syntax can be found in [FLASHBACK](../../../../Development Guide/SQL Reference Manual/SQL Statements/FLASHBACK.md#databaseclauses).

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the flashback operations in the CDB root and each PDB — along with their associated flashback log files, restore points, and other dependencies — are independent and private. Performing a flashback by connecting to the CDB root will only restore the CDB root itself and will not affect any PDBs.

## Dependency Mechanism



Database flashback leverages flashback log files for recovery. These logs record pre-modification data at the block level. Executing a flashback effectively rolls back changes by reverting the database to a specified time using incremental log and data changes.



<span id="DataScope" name="DataScope"></span>

## Recoverable Data Scope

Database flashback **only processes data** and does not roll back database configuration-related content (such as primary-standby roles, configuration parameter values, etc.). 

Database Flashback can recover most common data changes, such as additions, deletions, and modifications to database objects like tables, indexes, views, and stored procedures. However, the following limitations apply:

- Cannot flashback file-related DDL operations, such as tablespace DDL (e.g., CREATE/ALTER/DROP TABLESPACE statements) or datafile DDL (e.g., the database_file_clauses and double_write_file_clauses in ALTER DATABASE statement and the datafile_clause in ALTER TABLESPACE statement).

- Cannot flashback operations on archive/redo logs (e.g., switching/archiving redo logs, purging archive logs).

- Cannot flashback NOLOGGING tables.

- Cannot recover data lost due to storage media failures or OS-level accidental file deletions. 

- Flashback cannot clean up LSC table cold data physical files generated during the time span; these need to be manually deleted.  

<span id="TimeRange" name="TimeRange"></span>

## Recoverable Time Range

Theoretically, the earliest recoverable point is when database flashback was enabled, but the actual earliest recoverable SCN depends on the maximum value of the following SCNs:

- The earliest SCN stored in existing flashback log files, obtainable via the [V$FLASHBACK_DATABASE_LOG](../../../../Reference Manual/System Views/Dynamic Performance Views/V$FLASHBACK_DATABASE_LOG) view.

- The SCN or TIMESTAMP of the most recent execution of the following tablespace DDL operations, obtainable from [run.log](../../../Operation Monitoring/Log Management/Runtime Log Management).

    - CREATE TABLESPACE

    - ALTER TABLESPACE …… rename_clause

    - ALTER TABLESPACE OFFLINE|ONLINE

    - ALTER TABLESPACE …… ADD|DROP DATAFILE

- The SCN or TIMESTAMP of the most recent execution of the following file-related DDL operation, obtainable from [run.log](../../../Operation Monitoring/Log Management/Runtime Log Management).

    - ALTER DATABASE DATAFILE …… RESIZE

- When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the CDB root can only be flashed back to the SCN or TIMESTAMP of the most recent PDB addition/deletion DDL execution, obtainable from [run.log](../../../Operation Monitoring/Log Management/Runtime Log Management).

    - CREATE PLUGGABLE DATABASE

    - DROP PLUGGABLE DATABASE

    - DROP DATABASE executed executed on a PDB

> **Note**:
>
> If specific SCN or TIMESTAMP values cannot be obtained, attempt flashback directly. If the SCN exceeds the recoverable range, the error message will indicate the earliest allowable SCN.

## Flashback in Primary-Standby Deployment

In primary-standby HA deployments, 

- The database flashback functionality between the primary and standby databases/clusters is completely independent. The following content/configurations are not synchronized and do not interfere with each other:

    - Configuration of the database flashback functionality switch

    - Flashback logs

    - Restore points

    > **Note**:
    >
    > To simplify management, when the flashback functionality is planned as a rollback solution for misoperations, it is recommended that after enabling flashback and creating a restore point in the primary database, manually perform the same operations on each standby database synchronously.

- Flashback functionality and its characteristics for physical standby databases are as follows:

    - Before enabling the database flashback functionality and creating a restore point, the redo log apply needs to be paused first (ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL). If the target node is in the MOUNT stage, the apply has not started yet, and such operations can be directly performed by nature.

    - After performing a flashback, the redo log apply will be automatically paused and needs to be manually resumed (ALTER DATABASE RECOVER MANAGED STANDBY DATABASE).

    - After performing a flashback, archived log files and redo log files will not be cleared.

- Flashback functionality and its characteristics for logical standby databases are as follows:

    - Operations related to [basic configuration of database flashback](./Database Flashback Basic Configuration) and [management of restore points](./Managing Restore Points) cannot be carried out.
    
    - If the flashback-related configuration has been completed before converting to a logical standby database, the logical standby database only supports flashback through a restore point (FLASHBACK DATABASE TO RESTORE POINT ……), and after the flashback, its type will be determined according to the type at the flashback target time point (that is, it will become a physical standby database after the flashback).

    - After performing a flashback, archived log files and redo log files will not be cleared.
