This view displays the statistics of each SQL in the shared SQL area, including memory consumption, parsing, optimization, and execution information for the SQL statements.

|Field |Type |Description |
| --- | --- | --- |
| SQL_TEXT            | VARCHAR(1000)    | First 1000 characters of the SQL text               |
| SQL_FULLTEXT        | CLOB             | Full text of the SQL in CLOB format                  |
| SQL_ID              | VARCHAR(13)      | Unique ID value that identifies a SQL statement, derived from the hash/encryption of the SQL text |
| SHARABLE_MEM        | INTEGER          | Total shared memory occupied by this SQL and all version execution plans |
| PERSISTENT_MEM      | INTEGER          | Total memory actually used by this SQL and all version execution plans |
| RUNTIME_MEM         | INTEGER          | Total memory requested from the application heap during runtime for this SQL and all version execution plans |
| VERSION_COUNT       | INTEGER          | Number of execution plan versions                     |
| LOADED_VERSIONS     | INTEGER          | Number of loaded execution plan versions              |
| OPEN_VERSIONS       | INTEGER          | Number of execution plan versions in use              |
| USERS_OPENING       | INTEGER          | Number of users utilizing all execution plans         |
| USERS_EXECUTING     | INTEGER          | Number of users executing all plans                   |
| SORTS                | BIGINT           | Number of sorts                                      |
| FETCHES              | BIGINT           | Number of fetches                                    |
| EXECUTIONS           | BIGINT           | Number of executions                                  |
| PX_SERVERS_EXECUTIONS| BIGINT          | Number of executions by the parallel execution engine (reserved field) |
| END_OF_FETCH_COUNT| BIGINT           | Number of times reached the end of fetch; if currently in fetch or failed midway, statistics will not increase, thus this value should be less than or equal to the value of EXECUTIONS field |
| ROWS_PROCESSED      | BIGINT           | Number of rows processed                              |
| SERIALIZABLE_ABORTS | BIGINT           | Number of times transactions failed to serialize      |
| LOADS                | BIGINT           | Number of times execution plans have been loaded (reserved field) |
| INVALIDATIONS        | BIGINT           | Number of times execution plans have been invalidated  |
| PARSE_CALLS         | BIGINT           | Number of parse calls                                 |
| DISK_READS          | BIGINT           | Number of disk reads                                  |
| DIRECT_WRITES       | BIGINT           | Number of direct writes                               |
| DIRECT_READS        | BIGINT           | Number of direct reads                                |
| BUFFER_GETS         | BIGINT           | Number of times buffer is fetched from buffer        |
| IO_INTERCONNECT_BYTES| BIGINT         | Number of I/O interactions between the database and storage system |
| PHYSICAL_READ_REQUESTS| BIGINT        | Number of physical read requests                      |
| PHYSICAL_READ_BYTES| BIGINT           | Number of bytes read physically                       |
| PHYSICAL_WRITE_REQUESTS| BIGINT       | Number of physical write requests                     |
| PHYSICAL_WRITE_BYTES| BIGINT          | Number of bytes written physically                    |
| APPLICATION_WAIT_TIME| BIGINT         | Application wait time (unit: microseconds)          |
| CONCURRENCY_WAIT_TIME| BIGINT         | Concurrency wait time (unit: microseconds)          |
| CLUSTER_WAIT_TIME  | BIGINT           | Wait time between clusters (reserved field) (unit: microseconds) |
| USER_IO_WAIT_TIME | BIGINT           | User I/O wait time (unit: microseconds)             |
| PLSQL_EXEC_TIME    | BIGINT           | PL execution time (unit: microseconds), reserved field |
| CPU_TIME            | BIGINT           | CPU time for parsing, executing, fetching data (unit: microseconds) |
| ELAPSED_TIME        | BIGINT           | Time spent in parsing, executing, fetching data; includes execution time on DN side for ISC distributed clusters (unit: microseconds) |
| COMMAND_TYPE        | INTEGER          | Command type of the SQL<br/> * 1: SQL_QUERY  <br/> * 2: SQL_INSERT  <br/> * 3: SQL_UPDATE  <br/> * 4: SQL_DELETE  <br/> * 5: SQL_MERGE  <br/> * 6: SQL_WITH  <br/> * 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE      | VARCHAR(10)      | Reserved field                                       |
| OPTIMIZER_COST      | BIGINT           | Cost of the query as estimated by the optimizer      |
| OPTIMIZER_ENV       | RAW(2000)        | Reserved field                                       |
| OPTIMIZER_ENV_HASH_VALUE| BIGINT     | Reserved field                                       |
| PARSING_USER_ID    | INTEGER         | User ID of the first user to parse this SQL         |
| PARSING_SCHEMA_ID  | INTEGER         | Schema ID used to parse this SQL                     |
| PARSING_SCHEMA_NAME| VARCHAR(64)      | Schema name used to parse this SQL                   |
| ADDRESS              | RAW(8)           | SQL address                                          |
| HASH_VALUE          | BIGINT           | Hash value of the SQL, calculated from the SQL text  |
| PLAN_HASH_VALUE    | BIGINT           | Unique identifier for an execution plan              |
| MODULE               | VARCHAR(64)      | Reserved field                                       |
| MODULE_HASH         | BIGINT           | Reserved field                                       |
| ACTION               | VARCHAR(64)      | Reserved field                                       |
| ACTION_HASH         | BIGINT           | Reserved field                                       |
| OUTLINE_CATEGORY    | VARCHAR(64)      | If an outline is applied, this field shows the outline category; otherwise NULL |
| OUTLINE_SID         | INTEGER          | This field indicates whether the plan uses a public outline (NULL) or a private outline (corresponding session SID) |
| LAST_ACTIVE_CHILD_ADDRESS| RAW(8)   | Address of the last used execution plan              |
| LITERAL_HASH_VALUE | BIGINT           | Reserved field                                       |
| FIRST_LOAD_TIME    | DATE             | Time of the first load of the execution plan         |
| LAST_LOAD_TIME     | DATE             | Time of the last load                                 |
| LAST_ACTIVE_TIME    | DATE             | Time of the last activation of the execution plan    |
| IS_OBSOLETE         | VARCHAR(1)       | Indicates whether it is obsolete; might be obsolete if there are too many or too large execution plan versions |
| IS_BIND_SENSITIVE   | VARCHAR(1)      | Indicates whether it is sensitive to bind parameters; if the value of bind parameters changes, it needs to be re-optimized |
| BIND_DATA           | RAW(2000)        | Reserved field                                       |
| LOCKED_TOTAL        | BIGINT           | Number of times the execution plan has been locked; reserved field |
| PINNED_TOTAL        | BIGINT           | Number of times the execution plan has been pinned   |
| IS_REOPTIMIZABLE    | VARCHAR(1)       | Indicates whether it needs to be re-optimized        |
| RESTART_STATEMENTS   | BIGINT          | Number of statement restarts                          |
| FIRST_LOAD_LOCATION | BIGINT          | SQL address information                               |
| BLOCK_RECEIVED      | BIGINT           | Number of times the latest pages are received from other nodes in the cluster |
| CR_BLOCK_RECEIVED  | BIGINT           | Number of times the CR pages are received from other nodes in the cluster |
| LOCAL_GRANTS        | BIGINT           | Number of times local node has loaded authorized pages in the cluster |
| REMOTE_GRANTS       | BIGINT           | Number of times other nodes have loaded authorized pages in the cluster |
| LOCAL_UPGRADES      | BIGINT           | Number of times local node has upgraded page locks in the cluster |
| REMOTE_UPGRADES     | BIGINT           | Number of times other nodes have upgraded page locks in the cluster |