This view displays the basic performance statistics of SQL cursors. Each row represents data for a unique combination of SQL text and optimizer plan (i.e., a unique combination of SQL_ID and PLAN_HASH_VALUE).

The reserved field is currently empty.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                    | NUMBER    | Group ID                                         |
| GROUP_NODE_ID               | NUMBER    | Node ID within the group                         |
| INST_ID                     | NUMBER    | Instance ID                                     |
| SQL_TEXT                    | VARCHAR(1000) | First 1000 characters of SQL text               |
| SQL_FULLTEXT                | CLOB      | Full SQL text in CLOB format                    |
| SQL_ID                      | VARCHAR(13)  | ID value that uniquely identifies a SQL statement, obtained through hash/encryption of SQL text |
| LAST_ACTIVE_TIME            | DATE      | Time of the last access to this SQL             |
| LAST_ACTIVE_CHILD_ADDRESS    | RAW(8)   | Reserved field                                   |
| PLAN_HASH_VALUE             | BIGINT    | Unique identifier for an execution plan         |
| PARSE_CALLS                 | BIGINT    | Number of parse calls                            |
| DISK_READS                  | BIGINT    | Number of disk reads                             |
| DIRECT_WRITES               | BIGINT    | Number of direct writes by the cursor           |
| BUFFER_GETS                 | BIGINT    | Number of buffer reads                           |
| ROWS_PROCESSED              | BIGINT    | Total number of rows returned by the SQL statement |
| SERIALIZABLE_ABORTS         | BIGINT    | Number of times transactions could not be serialized |
| FETCHES                     | BIGINT    | Number of fetches for the SQL statement         |
| EXECUTIONS                  | BIGINT    | Number of executions after being loaded into buffer |
| END_OF_FETCH_COUNT          | BIGINT    | Number of times the cursor was fully executed after being brought into the buffer |
| LOADS                       | BIGINT    | Number of times the SQL enters the SQL pool |
| VERSION_COUNT               | INTEGER   | Number of versions of the execution plan        |
| INVALIDATIONS               | BIGINT    | Number of times the execution plan became invalid |
| PX_SERVERS_EXECUTIONS       | BIGINT    | Total number of executions performed in parallel |
| CPU_TIME                    | BIGINT    | CPU time used for parsing/execution/fetching (units: microseconds) |
| ELAPSED_TIME                | BIGINT    | Time consumed for parsing/execution/fetching (units: microseconds) |
| APPLICATION_WAIT_TIME       | BIGINT    | Wait time for applications (units: microseconds) |
| CONCURRENCY_WAIT_TIME       | BIGINT    | Concurrency wait time (units: microseconds)     |
| CLUSTER_WAIT_TIME           | BIGINT    | Cluster wait time (reserved field) (units: microseconds) |
| USER_IO_WAIT_TIME           | BIGINT    | User I/O wait time (units: microseconds)        |
| PLSQL_EXEC_TIME             | BIGINT    | PL execution time (units: microseconds), reserved field |
| JAVA_EXEC_TIME              | BIGINT    | Reserved field                                   |
| SORTS                       | BIGINT    | Number of completed sorts                        |
| SHARABLE_MEM                | INTEGER   | Size of shared memory used in the SQL pool (units: bytes) |
| TOTAL_SHARABLE_MEM          | INTEGER   | Total size of shared memory used in the SQL pool (units: bytes), equivalent to SHARABLE_MEM |
| BLOCK_RECEIVED              | BIGINT    | Number of times the latest pages were obtained from other nodes in the cluster |
| CR_BLOCK_RECEIVED           | BIGINT    | Number of times CR pages were obtained from other nodes in the cluster |
| LOCAL_GRANTS                | BIGINT    | Number of times local node granted page loads in the cluster |
| REMOTE_GRANTS               | BIGINT    | Number of times other nodes granted page loads in the cluster |
| LOCAL_UPGRADES              | BIGINT    | Number of times local node upgraded page locks in the cluster |
| REMOTE_UPGRADES             | BIGINT    | Number of times other nodes upgraded page locks in the cluster |