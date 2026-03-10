This view displays the basic performance statistics of SQL cursors, with each row representing a unique combination of SQL text and optimizer plan (i.e., the unique combination of SQL_ID and PLAN_HASH_VALUE).

The reserved fields are currently set to null.

|Field |Type |Description |
| --- | --- | --- |
| SQL_TEXT                   | VARCHAR(1000)| The first 1000 characters of the SQL text                      |
| SQL_FULLTEXT               | CLOB         | The full SQL text in CLOB format                               |
| SQL_ID                     | VARCHAR(13)  | The ID value that uniquely identifies a SQL statement, obtained through hashing/encrypting the SQL text |
| LAST_ACTIVE_TIME         | DATE         | The last access time of the SQL                                |
| LAST_ACTIVE_CHILD_ADDRESS| RAW(8)      | Reserved field                                                 |
| PLAN_HASH_VALUE          | BIGINT       | A unique identifier for an execution plan                      |
| PARSE_CALLS               | BIGINT       | Number of parse calls                                          |
| DISK_READS                 | BIGINT       | Number of disk reads                                          |
| DIRECT_WRITES             | BIGINT       | Number of times the cursor wrote directly                     |
| BUFFER_GETS               | BIGINT       | Number of times buffer was read                                |
| ROWS_PROCESSED            | BIGINT       | Total number of rows returned by the SQL statement             |
| SERIALIZABLE_ABORTS       | BIGINT       | Number of times the transaction failed to serialize            |
| FETCHES                    | BIGINT       | Number of fetches for the SQL statement                        |
| EXECUTIONS                 | BIGINT       | Number of executions after being loaded into buffer            |
| END_OF_FETCH_COUNT      | BIGINT       | Number of times the cursor executed completely after being brought to the buffer |
| LOADS                      | BIGINT       | Number of times the SQL entered the SQL buffer pool           |
| VERSION_COUNT             | INTEGER      | Number of versions of the execution plan                       |
| INVALIDATIONS               | BIGINT       | Number of times the execution plan became invalid              |
| PX_SERVERS_EXECUTIONS     | BIGINT       | Total number of executions performed in parallel               |
| CPU_TIME                  | BIGINT       | CPU usage time for parsing/execution/fetching, etc. (unit: microseconds) |
| ELAPSED_TIME              | BIGINT       | Time consumed for parsing/execution/fetching, etc. (unit: microseconds) |
| APPLICATION_WAIT_TIME    | BIGINT       | Application wait time (unit: microseconds)                    |
| CONCURRENCY_WAIT_TIME    | BIGINT       | Concurrency wait time (unit: microseconds)                    |
| CLUSTER_WAIT_TIME        | BIGINT       | Cluster wait time (reserved field) (unit: microseconds)       |
| USER_IO_WAIT_TIME       | BIGINT       | User I/O wait time (unit: microseconds)                       |
| PLSQL_EXEC_TIME          | BIGINT       | PL execution time (unit: microseconds), reserved field        |
| JAVA_EXEC_TIME           | BIGINT       | Reserved field                                                 |
| SORTS                      | BIGINT       | Number of completed sorts                                      |
| SHARABLE_MEM              | INTEGER      | Size of shared memory occupied in the SQL buffer pool (unit: bytes) |
| TOTAL_SHARABLE_MEM       | INTEGER      | Total size of shared memory occupied in the SQL buffer pool (unit: bytes), equivalent to SHARABLE_MEM |
| BLOCK_RECEIVED            | BIGINT       | Number of times the latest pages were fetched from other nodes in the cluster |
| CR_BLOCK_RECEIVED        | BIGINT       | Number of times CR pages were fetched from other nodes in the cluster |
| LOCAL_GRANTS              | BIGINT       | Number of times local node authorized page loads in the cluster |
| REMOTE_GRANTS             | BIGINT       | Number of times other nodes authorized page loads in the cluster |
| LOCAL_UPGRADES            | BIGINT       | Number of times local node authorized page lock upgrades in the cluster |
| REMOTE_UPGRADES           | BIGINT       | Number of times other nodes upgraded page locks in the cluster |