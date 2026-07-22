YStream is a logical log parsing interface provided by YashanDB, supporting a client API in Java.

Users can integrate the YStream API jar package into other software and call the API interface to perform Change Data Capture (CDC), retrieve and parse the additional logs of YashanDB, including DML and DDL.

No YStream functionality is available in ISC Distributed Cluster Deployment.

## Preparation

### Primary/Standby Environment Requirements

In Standalone Primary-Standby Deployment and Primary-Standby Cluster Deployment, when calling the [DBMS_YSTREAM_ADM](../PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM) advanced package to manage YStream services, the following constraints apply to the operator:

- The creation of YStream service (DBMS_YSTREAM_ADM.CREATE), table object configuration (DBMS_YSTREAM_ADM.ADD_TABLES and DBMS_YSTREAM_ADM.DROP_TABLES), parameter configuration (DBMS_YSTREAM_ADM.SET_PARAMETER), and deletion (DBMS_YSTREAM_ADM.DROP) can only be executed on the primary database/cluster.

- Generally, when starting (DBMS_YSTREAM_ADM.START) / stopping (DBMS_YSTREAM_ADM.STOP) YStream services, the principle of "the node that uses it starts it, the node that starts it stops it" should be followed:

    - In YAC/Distributed Cluster Deployment, the start and stop of the same YStream server are usually required to be operated on the same instance, unless that instance is unavailable (for example, offline), then stopping the YStream server on another instance is allowed.

    - The primary database/cluster always has the ability to stop YStream services started on the standby database/cluster, but is never allowed to stop YStream services started by the primary cluster instance on a standby cluster instance.

When running YStream services on the standby database/cluster, the following additional points need to be noted:

- Ensure that the network connection between the primary and standby database/cluster is fast and stable — otherwise, network disconnection errors may occur.

- Ensure that the compute and storage performance of the standby database/cluster is not lower than that of the primary database/cluster — otherwise, a large gap in log data may cause timeout errors.

- When primary/standby switching is performed, the YStream server running on the standby database/cluster may also experience network disconnection errors. To avoid this, it is recommended to stop the YStream server before performing the switchover or failover operation manually.

- When the primary database/cluster is shut down or exits abnormally, the YStream server running on the standby database/cluster may also experience network disconnection errors. To avoid this, it is recommended to stop the YStream server before shutting down the primary database/cluster.

### Database Archiving Management

YStream only supports parsing redo and archive log files generated under archiving mode. When using YStream, the database [archiving feature](../../Database Administration/Instance Management/Archive Management) must be enabled; otherwise, an error will occur when creating the YStream server.

### Database Additional Log Management

It is recommended to enable supplemental logging ([database-level](../SQL Reference Manual/SQL Statements/ALTER DATABASE.md#supplementallogclauses) or [table-level](../SQL Reference Manual/SQL Statements/ANALYZE TABLE.md#addsupplementalloggingclause)) before creating the YStream server. If not enabled, only supplemental information from existing logs can be parsed, and enabling supplemental logging after an existing YStream server will result in an error.

### Configuring the Database YStream Server

Operations for configuring the database YStream server need to be performed by users with the YSTREAM_CAPTURE role through the [DBMS_YSTREAM_ADM](../PL Reference Manual/Built-in Advanced PL Packages/DBMS_YSTREAM_ADM) advanced package. For specific operation procedures, refer to [YStream Usage Example](./YStream Usage Examples).

- The STREAM_POOL_SIZE value must be configured reasonably as needed before creating the YStream server.

- Please create the YStream server just before use and delete it promptly when no longer needed:

    - The number of YStream servers has an upper limit, and meaningless occupation should be minimized.

        - In Standalone Deployment, a maximum of 32 YStream servers can be created per instance.
        
        - In YAC/Distributed Cluster Deployment, a maximum of 32 YStream servers can be created per cluster, and the YStream servers can run on any instance in the cluster.

    - When creating a YStream server, the log parsing starting point is recorded. All unparsed archives after the log parsing starting point will not be automatically cleaned up. Timely delete unnecessary YStream servers to avoid preventing the database from automatically cleaning up archives, which would cause storage space waste.

    - If the `start_scn` of the target YStream server is too old (the UNDO log corresponding to the SCN has been cleared), a "snapshot too old" prompt will appear during startup. In case of such a situation, it is recommended to first delete the target YStream server, then recreate a YStream server with the same name and specify a new `start_scn`.

- The YStream server only parses logs after the `start_scn` specified at creation. Do not set `start_scn` in the middle of a DDL transaction; otherwise, YStream cannot construct metadata from incomplete DDL logs (the part after `start_scn`), which will cause subsequent logs related to that DDL to fail to parse and be ignored. After creating and starting the YStream server, you can execute DBMS_YSTREAM_ADM.TEST to verify whether the `start_scn` is reasonable; if the above abnormal scenario occurs, the verification process will report an error, and you need to recreate the YStream server and specify a suitable `start_scn`.

- The default parsing scope of the YStream server is all tables in the database. If you need to customize the parsing scope, you need to execute DBMS_YSTREAM_ADM.ADD_TABLES after creation to specify:

    - If you need to add existing tables to the parsing scope of a YStream server, it is not recommended to perform this operation when the server is in STARTED or RUNNING state. Otherwise, you must ensure that the target table does not have [table-level supplemental logging](../SQL Reference Manual/SQL Statements/ALTER TABLE.md#addsupplementalloggingclause) enabled before executing DBMS_YSTREAM_ADM.ADD_TABLES, and manually enable table-level supplemental logging for that table immediately after executing DBMS_YSTREAM_ADM.ADD_TABLES. Only after enabling it will the YStream server start parsing that table.

    - If the current parsing scope of a YStream server is all tables, it is not possible to specify specific parsing objects through DBMS_YSTREAM_ADM.ADD_TABLES (i.e., reduce the parsing scope) when it is in STARTED or RUNNING state. You need to stop the server first and then execute DBMS_YSTREAM_ADM.ADD_TABLES.

- If the YStream server is started on the standby database/cluster (i.e., DBMS_YSTREAM_ADM.START is executed on the standby database/cluster), you should wait for the standby database to replay the YStream system table logs until the YStream server is successfully started on the standby database/cluster (the STATUS field of the [V$YSTREAM_SERVER](../../Reference Manual/System Views/Dynamic Performance Views/V$YSTREAM_SERVER) view is STARTED), and then start parsing with the YStream API client. Otherwise, a YStream server status error may be reported.

- YStream API clients must have a one-to-one connection to the YStream server, and the client can only connect to the database node where the YStream server is started. The same YStream server cannot connect to multiple YStream API clients at the same time.

## Supported Data Synchronization

### HEAP Tables and TAC Tables

YStream supports parsing DDL, DML and primary key constraints of HEAP tables and TAC tables. The supported DDL scope is shown in the table below.

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

In addition, there are the following constraints for tables using some special features when YStream parses:

- For tables containing identity columns or auto-increment columns (unique to mysql mode), parsing and updating work properly only when the peer is YashanDB (mysql mode) or MySQL database. Otherwise, synchronization may fail.

- For tables containing identity columns, YStream cannot parse the table data.

- For HEAP tables containing virtual columns, YStream only supports parsing DDL, not DML.

- HEAP tables with encrypted columns and encrypted HEAP tables do not record supplemental logs, so YStream cannot parse encrypted tables.

- TAC tables with dictionary-encoded columns do not record supplemental logs, so YStream cannot parse TAC tables.


### Sequence

YStream only supports synchronizing Sequences in YashanDB (yashan mode), and the supported DDL scope is as follows:

|DDL Type |Synchronization Scope |Example |
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE                                   | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | CREATE SEQUENCE YDS.TEST_SEQ                                  |
| ALTER SEQUENCE                                    | Modified Sequence properties    | ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2                  |
| DROP SEQUENCE                                     | All properties of Sequence: hwm, max, min, cycle, order, cache, increment | DROP SEQUENCE YDS.TEST_SEQ                                   |

To synchronize the Sequence HWM (High Water Mark), enable `enableSequenceNextVal` in the YStream API to obtain the HWM update logic logs through the API. The synchronization scope includes the Sequence's schema, name, and current HWM.

In a YAC/Distributed Cluster, if the Sequence is set to NOORDER, YashanDB only guarantees the order of the Sequence within each instance. When synchronizing, YStream will parse the logs of all instances simultaneously; the parsed sequences may appear out of order or duplicate. To ensure the order of Sequences in a YAC, set the Sequence's [ORDER](../SQL Reference Manual/SQL Statements/CREATE SEQUENCE.md#sequence_order) property.

### Other DDL

YStream supports the synchronization of other DDL SQL statement strings.