YStream is a logical log parsing interface provided by YashanDB, supporting a client API in Java.

Users can integrate the YStream API jar package into other software and call the API interface to perform Change Data Capture (CDC), retrieve and parse the additional logs of YashanDB, including DML and DDL.

No YStream functionality is available in ISC Distributed Cluster Deployment.

## Preparation

### Primary/Standby Mode Environment Requirements

When running the YStream server on a standby cluster or standby database, please note:

- Ensure that the network connection between the primary and standby database/cluster is fast and stable — otherwise, network disconnection errors may occur.

- Ensure that the compute and storage performance of the standby database/cluster is not lower than that of the primary database/cluster — otherwise, a large gap in log data may cause timeout errors.

- When primary/standby switching is performed, the YStream server running on the standby database/cluster may also experience network disconnection errors. To avoid this, it is recommended to stop the YStream server before performing the switchover or failover operation manually.

- When the primary database/cluster is shut down or exits abnormally, the YStream server running on the standby database/cluster may also experience network disconnection errors. To avoid this, it is recommended to stop the YStream server before shutting down the primary database/cluster.

### Database Additional Log Management

It is recommended to enable supplemental logging ([database-level](../SQL Reference Manual/SQL Statements/ALTER DATABASE.html#supplementallogclauses) or [table-level](../SQL Reference Manual/SQL Statements/ALTER DATABASE.html#addsupplementalloggingclause)) before creating the YStream server. If not enabled, only supplemental information from existing logs can be parsed, and enabling supplemental logging after an existing YStream server will result in an error.

### Database Archiving Management

YStream only supports parsing redo and archive log files generated under archiving mode. When using YStream, the database [archiving feature](../../Database Administration/Instance Management/Archive Management) must be enabled; otherwise, an error will occur when creating the YStream server.

When creating the YStream server, the log parsing starting point will be recorded. Since the database has archiving enabled, all unparsed archives after the log parsing starting point will not be automatically cleaned up. If a specific YStream server's log parsing is no longer needed, please delete unnecessary YStream servers in a timely manner to avoid hindering the database's automated cleanup of archives and causing storage waste.

### Configuring the Database YStream Server

To create, configure, and start the YStream server, refer to the [YStream Usage Example](../PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM) for specific operations.

## Supported Data Synchronization

### HEAP Tables and TAC Tables

YStream supports DDL, DML, and primary key constraints for HEAP tables and TAC tables. The supported DDL scope is as follows:

|DDL Type |Synchronization Scope |Example |
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE TABLE                                      | Column names, column types, default values, primary keys | CREATE TABLE YDS.TEST(ID INT DEFAULT 1 PRIMARY KEY)         |
| ALTER TABLE RENAME xxx TO                          | -                          | ALTER TABLE RENAME YDS.TEST TO TEST1                         |
| DROP TABLE                                         | -                          | DROP TABLE YDS.TEST                                          |
| TRUNCATE TABLE                                     | -                          | TRUNCATE TABLE YDS.TEST                                      |
| ALTER TABLE ADD COLUMN                            | Column names, column types, default values, primary keys | ALTER TABLE YDS.TEST ADD COLUMN COL0 INT DEFAULT 1 PRIMARY KEY |
| ALTER TABLE DROP COLUMN                            | -                          | ALTER TABLE YDS.TEST DROP COLUMN ID                          |
| ALTER TABLE RENAME COLUMN                          | -                          | ALTER TABLE YDS.TEST RENAME COLUMN ID TO ID1                 |
| ALTER TABLE MODIFY                                | Column types, default values, primary keys | ALTER TABLE YDS.TEST MODIFY (ID INT DEFAULT 1 PRIMARY KEY)  |
| ALTER TABLE ADD PRIMARY KEY                        | -                          | ALTER TABLE YDS.TEST ADD PRIMARY KEY (ID)                    |
| ALTER TABLE ADD CONSTRAINT xxx PRIMARY KEY         | -                          | ALTER TABLE YDS.TEST ADD CONSTRAINT PK PRIMARY KEY (ID)      |
| ALTER TABLE DROP PRIMARY KEY                       | -                          | ALTER TABLE YDS.TEST DROP PRIMARY KEY                        |
| ALTER TABLE DROP CONSTRAINT primary_key_constraint| -                               | ALTER TABLE YDS.TEST DROP CONSTRAINT PK (Constraint named `PK` is the primary key constraint) |

> **Note**: 
>
> - When a table contains an identity column or auto-increment column (mysql mode), YStream cannot parse the data of that table.
> - When a table contains a virtual column, additional logs for DML operations are not recorded. YStream can only parse DDL statements for such tables and cannot parse DML operations.

### Sequence

YStream supports the synchronization of Sequences, with the following DDL scope:

|DDL Type |Synchronization Scope |Example |
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE                                   | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | CREATE SEQUENCE YDS.TEST_SEQ                                  |
| ALTER SEQUENCE                                    | Modified Sequence properties    | ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2                  |
| DROP SEQUENCE                                     | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | DROP SEQUENCE YDS.TEST_SEQ                                   |

To synchronize the Sequence HWM (High Water Mark), enable `enableSequenceNextVal` in the YStream API to obtain the HWM update logic logs through the API. The synchronization scope includes the Sequence's schema, name, and current HWM.

In a YAC/Distributed Cluster, if the Sequence is set to NOORDER, YashanDB only guarantees the order of the Sequence within each instance. When synchronizing, YStream will parse the logs of all instances simultaneously; the parsed sequences may appear out of order or duplicate. To ensure the order of Sequences in a YAC, set the Sequence's [ORDER](../SQL Reference Manual/SQL Statements/CREATE SEQUENCE.html#sequence_order) property.

### Other DDL

YStream supports the synchronization of other DDL SQL statement strings.