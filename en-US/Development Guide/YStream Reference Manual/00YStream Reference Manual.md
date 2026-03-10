YStream is the logical log parsing interface provided by YashanDB, and the client API is implemented in Java code.

Client software can integrate the YStream API jar package and call the API interface to obtain the logical logs from a YashanDB database environment where supplemental logging ([database level](../SQL Reference Manual/SQL Statements (yashan Mode)/ALTER DATABASE.html#supplementallogclauses) or [table level](../SQL Reference Manual/SQL Statements (yashan Mode)/ALTER DATABASE.html#addsupplementalloggingclause)) has been enabled, including DML and DDL.

YStream is not suitable for ISC Distributed Cluster Deployment.

## Preparation

### Primary/Standby Mode Environment Requirements

When using YStream on a standby database, ensure that the network connection between primary/standby instances or primary/standby clusters is fast and stable. Otherwise, there may be network disconnection exceptions; the computing and storage performance of the standby instance should not be lower than that of the primary instance. Otherwise, a significant log disparity between primary/standby instances may lead to timeout exceptions. The YStream service of the switchover standby instance will reconnect after being disconnected from the primary instance. The standby instance's YStream may experience network disconnection exceptions, so it is recommended to perform the switchover operation after stopping the YStream service. If the primary instance shuts down or exits abnormally for other reasons, the standby instance's YStream may also experience network disconnection exceptions. It is advised to execute the shutdown operation on the primary instance after stopping the YStream service.

### Database Archiving Management

YStream only supports parsing redo and archive log files generated under archiving mode. When using YStream, the database archiving feature must be enabled; otherwise, an error will occur when creating the YStream service.

When creating the YStream service, the log parsing starting point will be recorded. Since the database has archiving enabled, all unparsed archives after the log parsing starting point will not be automatically cleaned up. If a specific service's log parsing is no longer needed, please delete unnecessary YStream services in a timely manner to avoid hindering the database's automated cleanup of archives and causing storage waste.

### Configuring the Database YStream Server

Refer to the [YStream Client Usage Example](YStream Client Usage Introduction) to configure the YStream Server, ensuring that the YStream service is created and started before beginning parsing via the YStream API.

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

### Sequence

YStream supports the synchronization of Sequences, with the following DDL scope:

|DDL Type |Synchronization Scope |Example |
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE                                   | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | CREATE SEQUENCE YDS.TEST_SEQ                                  |
| ALTER SEQUENCE                                    | Modified Sequence properties    | ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2                  |
| DROP SEQUENCE                                     | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | DROP SEQUENCE YDS.TEST_SEQ                                   |

To synchronize the Sequence HWM (High Water Mark), enable `enableSequenceNextVal` in the YStream API to obtain the HWM update logic logs through the API. The synchronization scope includes the Sequence's schema, name, and current HWM.

In a YAC, if the Sequence is set to NOORDER, YashanDB only guarantees the order of the Sequence within each instance. When synchronizing, YStream will parse the logs of all instances simultaneously; the parsed sequences may appear out of order or duplicate. To ensure the order of Sequences in a YAC, set the Sequence's [ORDER](../SQL Reference Manual/SQL Statements (yashan Mode)/CREATE SEQUENCE.html#sequence_order) property.

### Other DDL
YStream supports the synchronization of other DDL SQL statement strings.