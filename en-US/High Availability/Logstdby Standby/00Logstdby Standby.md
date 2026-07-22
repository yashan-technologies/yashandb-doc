The implementation process of replication involves the primary database sending redo logs to the standby database. The standby database processes and applies the redo logs accordingly, achieving online synchronization between the standby database and the primary database. Depending on the log application mechanism, replication is divided into physical replication and logical replication.

| Replication Mode  | Function Description  | Configuration Method  |
|--------------------|----------------------|----------------|
| Physical Replication | After the standby database receives the redo logs, it directly applies the redo logs to update the data pages of the standby database, making the data of the standby database completely consistent with that of the primary database. This method is called physical replication, and the standby database can be referred to as a physical standby database. | In a [High Availability](../Overview of YashanDB High Availability) deployment, physical replication between primary and standby databases is fully automated and does not require manual configuration. |
| Logical Replication | After the standby database receives the redo logs, it parses the redo logs into SQL statements, and then executes these SQL statements to keep the data of the standby database logically consistent with that of the primary database. This method is called logical replication, and the standby database can be referred to as a logical standby database.  | After completing the primary/standby deployment, the standby database needs to undergo a type conversion (defaulting to physical standby database, it needs to be converted to a logical standby database), and manual activation of logical apply among other operations. For more details, please refer to [Configuring Logstdby Standby](Configuring Logstdby Standby).|



Logical replication feature applies to Standalone/YAC/Distributed Cluster Deployment, but in YAC Deployment, it applies only to rolling upgrade.


To standardize terminology, in YAC Deployment, "primary database" refers to the primary cluster, and "standby database" refers to the standby cluster.

<span id="scope" name="scope"></span>

## Scope of Logical Replication

Objects that can be synchronized via logical replication are primarily HEAP tables in the primary database containing fields of data types other than JSON, BFILE, and UDT (including built-in UDTs such as XMLTYPE, ST_GEOMETRY, etc.). The following content will **NOT** be synchronized:

- All business from the SYS user in the primary database

- Audit policies

- Privileges and roles

- PROFILE

- Users (supports synchronization of DROP USER)

- Tablespaces

- Data files

- Dual writing files

- OUTLINE

- DATABASE LINK

- AC

- DIRECTORY

- HWM of SEQUENCE

- LSC tables, TAC tables, external tables, encrypted tables

> **Note**: 
>
> - For HEAP table with virtual columns, the logical standby database can only synchronize can only synchronize DDL statements, not the data.
> - The logical standby database can only synchronize HEAP tables with identity column or auto-increment column (mysql mode) from primary database.

<span id="requirement" name="requirement"></span>

## Operating Requirements for Logical Standby Database

The logical standby database is typically in a read-write state. If write operations need to be performed on the logical standby database, the SYS user must be used.

The requirements for successfully deploying and operating a logical standby database are as follows:

- The logical standby database does not allow cascade deployment.

- The logical standby database cannot enable self-selected master.

- If the logical standby database needs to execute failover, all other primary standby databases need to be rebuilt after completion.

When there is a logical standby database, the following constraints apply to operations on the primary database:
  
- The primary database cannot use cursors; otherwise, the logical standby database may become inconsistent with the data in the primary database.

- The primary database is not allowed to update primary key columns; otherwise, the logical standby database may become inconsistent with the data in the primary database.

- The primary database is not allowed to execute table data flashbacks; otherwise, the logical standby database may become inconsistent with the data in the primary database.

- The primary database must not manually disable library-level additional logging; otherwise, it will cause exceptions in the logical standby database, preventing normal synchronization.

- It must be ensured that each row in the primary database tables can be uniquely identified. It is recommended to create primary keys or unique constraints/indexes in the tables. When primary keys or unique constraints/indexes exist, the UPDATE statement will record the necessary column values in the redo log to uniquely identify the modified target row, and unique constraints/indexes help improve the efficiency of applying UPDATE statements in the logical standby database, avoiding/reducing full table scans.

  - When the table defines a primary key, the additional logging of the UPDATE statement will record the primary key and the modified columns as identifiers for the target row.

  - If the table does not define a primary key, the shortest non-null unique constraint/index and the modified columns will be recorded as identifiers for the target row.

  - If neither primary keys nor non-null unique constraints/indexes are defined, all declared columns with a maximum length of 8000 bytes will be recorded as identifiers for the target row. In this case, the logical standby database may not correctly maintain data for the following data types:
  
    - LOB types
    
    - VARCHAR (Size > 8000 bytes), NVARCHAR (Size > 8000 bytes)

    - RAW (Size > 8000 bytes)

  - If data rows do not have uniqueness, the logical standby database may not correctly maintain the corresponding data.
