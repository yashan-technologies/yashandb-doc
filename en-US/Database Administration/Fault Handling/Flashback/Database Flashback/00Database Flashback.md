Database flashback allows rolling back data to a historical point-in-time by reverting changes to database objects, without traditional backup restoration. It functions similarly to Point-in-Time Recovery (PITR) but is more efficient and streamlined, meeting stricter RPO and RTO requirements.

The SQL statement is FLASHBACK DATABASE TO ……, detailed syntax can be found in [FLASHBACK](../../../../Development Guide/SQL Reference Manual/SQL Statements/FLASHBACK.md#databaseclauses).

## Dependency Mechanism



Database flashback leverages flashback log files for recovery. These logs record pre-modification data at the block level. Executing a flashback effectively rolls back changes by reverting the database to a specified time using incremental log and data changes.



<span id="DataScope" name="DataScope"></span>

## Recoverable Data Scope

Database Flashback can recover most common changes, such as additions, deletions, and modifications to database objects like tables, indexes, views, and stored procedures. However, the following limitations apply:

- Cannot flashback file-related DDL operations, such as tablespace DDL (e.g., CREATE/ALTER/DROP TABLESPACE statements) or datafile DDL (e.g., the database_file_clauses and double_write_file_clauses in ALTER DATABASE statement and the datafile_clause in ALTER TABLESPACE statement).

- Cannot flashback operations on archive/redo logs (e.g., switching/archiving redo logs, purging archive logs).

- Cannot flashback NOLOGGING objects.

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

> **Note**:
>
> If specific SCN or TIMESTAMP values cannot be obtained, attempt flashback directly. If the SCN exceeds the recoverable range, the error message will indicate the earliest allowable SCN.
