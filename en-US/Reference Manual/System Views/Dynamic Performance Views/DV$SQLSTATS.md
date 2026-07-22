This view displays a summary of SQL execution plan statistics for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                    | INTEGER   | Group ID                                                                    |
| GROUP_NODE_ID               | INTEGER   | Node ID within the group                                                    |
| SQL_TEXT                    | VARCHAR(1000) | The first 1000 characters of the SQL text                                   |
| SQL_FULLTEXT                | CLOB      | Full character SQL in CLOB format                                           |
| SQL_ID                      | VARCHAR(13) | The unique ID value identifying a SQL statement, calculated through hashing/encryption of the SQL text |
| LAST_ACTIVE_TIME            | DATE      | The last time this SQL was accessed                                         |
| LAST_ACTIVE_CHILD_ADDRESS    | RAW(8)    | Reserved field                                                              |
| PLAN_HASH_VALUE             | BIGINT    | A unique identifier for an execution plan                                   |
| PARSE_CALLS                 | BIGINT    | Number of parse calls                                                       |
| DISK_READS                  | BIGINT    | Number of disk reads                                                        |
| DIRECT_WRITES               | BIGINT    | Number of direct writes by the cursor (reserved field)                       |
| BUFFER_GETS                 | BIGINT    | Number of reads from the buffer                                             |
| ROWS_PROCESSED              | BIGINT    | Total number of rows returned by the SQL statement                          |
| SERIALIZABLE_ABORTS         | BIGINT    | Number of times a transaction failed to be serialized                      |
| FETCHES                     | BIGINT    | Number of fetches for the SQL statement                                     |
| EXECUTIONS                  | BIGINT    | Number of executions after being loaded into the buffer pool                |
| END_OF_FETCH_COUNT          | BIGINT    | Number of times the cursor fully executed after being brought to the library buffer |
| LOADS                       | BIGINT    | Number of times SQL entered the SQL pool                             |
| VERSION_COUNT               | INTEGER   | Number of versions for the execution plan                                    |
| INVALIDATIONS               | BIGINT    | Number of times execution plans were invalidated                            |
| PX_SERVERS_EXECUTIONS       | BIGINT    | Total number of executions performed in parallel (reserved field)                           |
| CPU_TIME                    | BIGINT    | CPU usage time for parsing/executing/fetching, etc. (Unit: microseconds)   |
| ELAPSED_TIME                | BIGINT    | Consumption time for parsing/executing/fetching, etc. (Unit: microseconds) |
| APPLICATION_WAIT_TIME       | BIGINT    | Application wait time (Unit: microseconds)                                 |
| CONCURRENCY_WAIT_TIME       | BIGINT    | Concurrency wait time (Unit: microseconds)                                 |
| CLUSTER_WAIT_TIME           | BIGINT    | Cluster wait time (Reserved field) (Unit: microseconds)                   |
| USER_IO_WAIT_TIME           | BIGINT    | User I/O wait time (Unit: microseconds)                                    |
| PLSQL_EXEC_TIME             | BIGINT    | PL execution time (Unit: microseconds), reserved field                     |
| JAVA_EXEC_TIME              | BIGINT    | Reserved field                                                              |
| SORTS                       | BIGINT    | Number of sorts completed                                                  |
| SHARABLE_MEM                | INTEGER   | Size of shared memory occupied in the SQL pool (Unit: bytes)        |
| TOTAL_SHARABLE_MEM          | INTEGER   | Total size of shared memory occupied in the SQL pool (Unit: bytes), equivalent to SHARABLE_MEM |
