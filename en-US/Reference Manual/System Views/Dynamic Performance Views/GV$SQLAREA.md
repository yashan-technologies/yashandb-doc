This view shows the statistics of each SQL in the shared SQL area, including memory consumption, parsing, optimization, and execution information for the SQL statement.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                 | NUMBER     | Group ID                                                   |
| GROUP_NODE_ID            | NUMBER     | Node ID within the group                                   |
| INST_ID                  | NUMBER     | Instance ID                                                |
|  SQL_TEXT    | VARCHAR(1000) | The first 1000 characters of the SQL text                  |
| SQL_FULLTEXT             | CLOB       | Full SQL in CLOB format                                    |
| SQL_ID                   | VARCHAR(13) | ID value that uniquely identifies a SQL statement, calculated by hashing/encryption of SQL text |
| SHARABLE_MEM             | INTEGER    | Total shared memory used by this SQL and all versions of the execution plans |
| PERSISTENT_MEM           | INTEGER    | Total memory actually used by this SQL and all versions of the execution plans |
| RUNTIME_MEM             | INTEGER  |  The memory actually used by this SQL during execution (in bytes) (reserved field) |
| VERSION_COUNT            | INTEGER    | Number of execution plan versions                           |
| LOADED_VERSIONS          | INTEGER    | Number of loaded execution plan versions                    |
| OPEN_VERSIONS            | INTEGER    | Number of execution plan versions currently in use          |
| USERS_OPENING            | INTEGER    | Number of users utilizing all execution plans               |
| USERS_EXECUTING          | INTEGER    | Number of users currently executing all plans               |
| SORTS                    | BIGINT     | Number of sorts                                            |
| FETCHES                  | BIGINT     | Number of fetches                                         |
| EXECUTIONS               | BIGINT     | Number of executions                                       |
| PX_SERVERS_EXECUTIONS    | BIGINT     | Execution count of the parallel execution engine (reserved field) |
| END_OF_FETCH_COUNT       | BIGINT     | Number of times fetch reached the end. If currently fetching or if fetching fails, this will not increment, hence its value should be less than or equal to EXECUTIONS field value |
| ROWS_PROCESSED           | BIGINT     | Number of rows processed                                    |
| SERIALIZABLE_ABORTS      | BIGINT     | Number of transactions that failed to serialize            |
| LOADS                    | BIGINT     | Number of times execution plans were loaded  |
| INVALIDATIONS            | BIGINT     | Number of execution plan invalidations                      |
| PARSE_CALLS              | BIGINT     | Number of parses                                          |
| DISK_READS               | BIGINT     | Number of disk reads                                       |
| DIRECT_WRITES            | BIGINT     | Number of direct writes (reserved field)                                    |
| DIRECT_READS             | BIGINT     | Number of direct reads (reserved field)                                    |
| BUFFER_GETS              | BIGINT     | Number of times buffers were gotten from the buffer       |
| IO_INTERCONNECT_BYTES     | BIGINT     | Number of I/O interactions between the database and storage system |
| PHYSICAL_READ_REQUESTS   | BIGINT     | Number of physical read requests                           |
| PHYSICAL_READ_BYTES      | BIGINT     | Number of bytes read physically                            |
| PHYSICAL_WRITE_REQUESTS   | BIGINT     | Number of physical write requests                          |
| PHYSICAL_WRITE_BYTES     | BIGINT     | Number of bytes written physically                         |
| APPLICATION_WAIT_TIME    | BIGINT     | Application wait time (unit: microseconds)                |
| CONCURRENCY_WAIT_TIME    | BIGINT     | Concurrency wait time (unit: microseconds)                |
| CLUSTER_WAIT_TIME        | BIGINT     | Wait time between clusters (reserved field) (unit: microseconds) |
| USER_IO_WAIT_TIME        | BIGINT     | User I/O wait time (unit: microseconds)                  |
| PLSQL_EXEC_TIME          | BIGINT     | PL execution time (unit: microseconds), reserved field   |
| CPU_TIME                 | BIGINT     | CPU time for parsing, execution, and fetching data (unit: microseconds) |
| ELAPSED_TIME             | BIGINT     | Time taken for parsing, execution, and fetching data. If in an ISC distributed cluster, includes the execution time on the DN side (unit: microseconds) |
| COMMAND_TYPE             | INTEGER    | Command type of SQL                                       <br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE           | VARCHAR(10) | Reserved field                                           |
| OPTIMIZER_COST           | BIGINT     | Cost of the query provided by the optimizer               |
| OPTIMIZER_ENV            | RAW(2000)  | Reserved field                                           |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT     | Reserved field                                           |
| PARSING_USER_ID         | INTEGER    | User ID that first parsed the SQL                        |
| PARSING_SCHEMA_ID       | INTEGER    | Schema ID used when parsing the SQL                      |
| PARSING_SCHEMA_NAME     | VARCHAR(64) | Schema name used when parsing the SQL                    |
| ADDRESS                  | RAW(8)    | SQL address                                              |
| HASH_VALUE               | BIGINT     | Hash value of the SQL, obtained from SQL text             |
| PLAN_HASH_VALUE          | BIGINT     | Unique identifier of an execution plan                    |
| MODULE                   | VARCHAR(64) |  The name of the module in which the SQL statement is currently executing. This module name is set by calling DBMS_APPLICATION_INFO.SET_MODULE                                          |
| MODULE_HASH              | BIGINT     |   The hash value of the module name                                          |
| ACTION                   | VARCHAR(64) |   The name of the action being executed when the SQL statement is first parsed. This action name is set by invoking DBMS_APPLICATION_INFO.SET_ACTION                                           |
| ACTION_HASH              | BIGINT     |   Hash value of the operation name                                           |
| OUTLINE_CATEGORY         | VARCHAR(64) | If an outline is applied, this field represents the outline category; otherwise, it is NULL |
| OUTLINE_SID              | INTEGER    | Reserved field |
| LAST_ACTIVE_CHILD_ADDRESS | RAW(8)    | Address of the last used execution plan                  |
| LITERAL_HASH_VALUE       | BIGINT     | Reserved field                                           |
| FIRST_LOAD_TIME          | DATE       | Timestamp of when the execution plan was first loaded    |
| LAST_LOAD_TIME           | DATE       | Timestamp of last load                                    |
| LAST_ACTIVE_TIME         | DATE       | Timestamp of the last activation of the execution plan    |
| IS_OBSOLETE              | VARCHAR(1) | Indicates if it is obsolete; may become obsolete if there are too many or too large execution plan versions |
| IS_BIND_SENSITIVE        | VARCHAR(1) | Indicates if it is sensitive to bind parameters; if bind parameter values change, it needs to be re-optimized |
| BIND_DATA                | RAW(2000)  | Reserved field                                           |
| LOCKED_TOTAL             | BIGINT     | Number of times the execution plan was locked (reserved field) |
| PINNED_TOTAL             | BIGINT     | Number of times the execution plan was pinned            |
| IS_REOPTIMIZABLE         | VARCHAR(1) | Indicates if it needs re-optimization                    |
| RESTART_STATEMENTS       | BIGINT     | Count of statement restarts                               |
| FIRST_LOAD_LOCATION      | BIGINT     | SQL address information                                   |
| BLOCK_RECEIVED           | BIGINT     | Number of times latest pages were received from other nodes in the cluster |
| CR_BLOCK_RECEIVED        | BIGINT     | Number of times CR pages were received from other nodes in the cluster |
| LOCAL_GRANTS             | BIGINT     | Number of times local node authorized page loads in the cluster |
| REMOTE_GRANTS            | BIGINT     | Number of times other nodes authorized page loads in the cluster |
| LOCAL_UPGRADES           | BIGINT     | Number of times local node authorized page lock upgrades in the cluster |
| REMOTE_UPGRADES          | BIGINT     | Number of times other nodes authorized page lock upgrades in the cluster |
