This view shows the statistics of each SQL in the shared SQL area across each node in the ISC distributed cluster, including memory consumption, parsing, optimization, and execution information for the SQL in the statement. The same CN executing the same SQL shows one entry, while different CNs executing the same SQL show multiple entries.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                | INTEGER  | Group ID                                                                                |
| GROUP_NODE_ID           | INTEGER  | Node ID within the group                                                               |
| SQL_TEXT                | VARCHAR(1000) | The first 1000 characters of the SQL statement                                      |
| SQL_FULLTEXT            | CLOB     | The full character of the SQL in CLOB format                                          |
| SQL_ID                  | VARCHAR(13) | The ID value that uniquely identifies a SQL statement, computed using a hash/encryption method on the SQL text |
| SHARABLE_MEM            | INTEGER  | Total shared pages occupied by this SQL and all versions of its execution plans        |
| PERSISTENT_MEM          | INTEGER  | Total memory actually used by this SQL and all versions of its execution plans       |
| RUNTIME_MEM             | INTEGER  | Total memory requested from AppAreaPool at runtime for this SQL and all versions of its execution plans |
| VERSION_COUNT           | INTEGER  | Number of execution plan versions                                                       |
| LOADED_VERSIONS         | INTEGER  | Number of loaded execution plan versions                                                |
| OPEN_VERSIONS           | INTEGER  | Number of execution plan versions currently in use                                      |
| USERS_OPENING           | INTEGER  | Number of users using all execution plans                                               |
| USERS_EXECUTING         | INTEGER  | Number of users currently executing all plans                                           |
| SORTS                   | BIGINT   | Number of sorts                                                                          |
| FETCHES                 | BIGINT   | Number of fetches                                                                         |
| EXECUTIONS              | BIGINT   | Number of executions                                                                      |
| PX_SERVERS_EXECUTIONS   | BIGINT   | Number of executions by the parallel execution engine                                     |
| END_OF_FETCH_COUNT      | BIGINT   | Number of times fetched to the end; if still fetching or failed midway, this does not increase statistics. Thus, its value should be less than or equal to the EXECUTIONS field value |
| ROWS_PROCESSED          | BIGINT   | Number of rows processed                                                                  |
| SERIALIZABLE_ABORTS     | BIGINT   | Number of times transactions failed to serialize                                         |
| LOADS                   | BIGINT   | Number of times execution plans were loaded                                             |
| INVALIDATIONS           | BIGINT   | Number of times execution plans were invalidated                                         |
| PARSE_CALLS             | BIGINT   | Number of times parsed                                                                    |
| DISK_READS              | BIGINT   | Number of disk reads                                                                      |
| DIRECT_WRITES           | BIGINT   | Number of direct writes                                                                   |
| DIRECT_READS            | BIGINT   | Number of direct reads                                                                    |
| BUFFER_GETS             | BIGINT   | Number of times buffers were obtained from buffer                                        |
| IO_INTERCONNECT_BYTES    | BIGINT   | Number of I/O interactions between the database and storage system                       |
| PHYSICAL_READ_REQUESTS  | BIGINT   | Number of physical read requests                                                          |
| PHYSICAL_READ_BYTES     | BIGINT   | Number of bytes read physically                                                           |
| PHYSICAL_WRITE_REQUESTS | BIGINT   | Number of physical write requests                                                         |
| PHYSICAL_WRITE_BYTES    | BIGINT   | Number of bytes written physically                                                        |
| APPLICATION_WAIT_TIME    | BIGINT   | Application wait time (unit: microseconds)                                              |
| CONCURRENCY_WAIT_TIME    | BIGINT   | Concurrency wait time (unit: microseconds)                                              |
| CLUSTER_WAIT_TIME        | BIGINT   | Inter-cluster wait time (reserved field) (unit: microseconds)                          |
| USER_IO_WAIT_TIME        | BIGINT   | User I/O wait time (unit: microseconds)                                                |
| PLSQL_EXEC_TIME         | BIGINT   | PL execution time (unit: microseconds), reserved field                                  |
| CPU_TIME                | BIGINT   | CPU time for parsing, executing, and fetching data (unit: microseconds)                |
| ELAPSED_TIME            | BIGINT   | Time spent on parsing, executing, and fetching data; if in an ISC distributed cluster, includes execution time on the DN side (unit: microseconds) |
| COMMAND_TYPE            | INTEGER  | SQL command type <br/> * 1: SQL_QUERY  <br/> * 2: SQL_INSERT  <br/> * 3: SQL_UPDATE  <br/> * 4: SQL_DELETE  <br/> * 5: SQL_MERGE  <br/> * 6: SQL_WITH  <br/> * 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE          | VARCHAR(10) | Reserved field                                                                        |
| OPTIMIZER_COST          | BIGINT   | Cost of this query as given by the optimizer                                            |
| OPTIMIZER_ENV           | RAW(2000) | Reserved field                                                                          |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT   | Reserved field                                                                          |
| PARSING_USER_ID        | INTEGER  | User ID of the first user to parse this SQL                                            |
| PARSING_SCHEMA_ID      | INTEGER  | Schema ID used when parsing this SQL                                                    |
| PARSING_SCHEMA_NAME    | VARCHAR(64) | Schema name used when parsing this SQL                                                |
| ADDRESS                 | RAW(8)   | SQL address                                                                             |
| HASH_VALUE              | BIGINT   | Hash value of the SQL calculated by the SQL text                                        |
| PLAN_HASH_VALUE         | BIGINT   | Unique identifier for an execution plan                                                |
| MODULE                  | VARCHAR(64) | Reserved field                                                                          |
| MODULE_HASH             | BIGINT   | Reserved field                                                                          |
| ACTION                  | VARCHAR(64) | Reserved field                                                                          |
| ACTION_HASH             | BIGINT   | Reserved field                                                                          |
| OUTLINE_CATEGORY        | VARCHAR(64) | If an outline is applied, this field indicates the outline category; otherwise NULL    |
| OUTLINE_SID             | INTEGER  | This field indicates whether the plan uses a public outline (NULL) or a private outline (session SID) |
| LAST_ACTIVE_CHILD_ADDRESS | RAW(8) | Address of the last used execution plan                                                  |
| LITERAL_HASH_VALUE      | BIGINT   | Reserved field                                                                          |
| FIRST_LOAD_TIME         | DATE     | Time when the execution plan was first loaded                                          |
| LAST_LOAD_TIME          | DATE     | Time of the last load                                                                    |
| LAST_ACTIVE_TIME        | DATE     | Time when the execution plan was last activated                                          |
| IS_OBSOLETE             | VARCHAR(1) | Whether it is obsolete; if the execution plan versions are too many or too large, it may be obsolete |
| IS_BIND_SENSITIVE       | VARCHAR(1) | Whether it is sensitive to binding parameters; if the binding parameter values change, it needs to be re-optimized |
| BIND_DATA               | RAW(2000) | Reserved field                                                                          |
| LOCKED_TOTAL            | BIGINT   | Number of times the execution plan was locked, reserved field                          |
| PINNED_TOTAL            | BIGINT   | Number of times the execution plan was pinned                                           |
| IS_REOPTIMIZABLE        | VARCHAR(1) | Whether it needs to be re-optimized                                                     |
| RESTART_STATEMENTS      | BIGINT   | Number of times the statement was restarted                                             |
| FIRST_LOAD_LOCATION     | BIGINT   | SQL address information                                                                  |
| PROGRAM_ID               | BIGINT     | Object ID of the program that first parsed this SQL |
| PROGRAM_LINE#            | INTEGER    | Line number in the program where this SQL was first parsed |