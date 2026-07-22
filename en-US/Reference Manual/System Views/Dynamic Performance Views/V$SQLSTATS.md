This view displays the basic performance statistics of SQL cursors, with each row representing a unique combination of SQL text and optimizer plan (i.e., the unique combination of SQL\_ID and PLAN\_HASH\_VALUE).

The reserved fields are currently set to null.

|Field |Type |Description |
| --- | --- | --- |
| SQL\_TEXT                   | VARCHAR(1000)| The first 1000 characters of the SQL text                      |
| SQL\_FULLTEXT               | CLOB         | The full SQL text in CLOB format                               |
| SQL\_ID                     | VARCHAR(13)  | The ID value that uniquely identifies a SQL statement, obtained through hashing/encrypting the SQL text |
| LAST\_ACTIVE\_TIME         | DATE         | The last access time of the SQL                                |
| LAST\_ACTIVE\_CHILD\_ADDRESS| RAW(8)      | Reserved field                                                 |
| PLAN\_HASH\_VALUE          | BIGINT       | A unique identifier for an execution plan                      |
| PARSE\_CALLS               | BIGINT       | Number of parse calls                                          |
| DISK\_READS                 | BIGINT       | Number of disk reads                                          |
| DIRECT\_WRITES             | BIGINT       | Number of times the cursor wrote directly                     |
| BUFFER\_GETS               | BIGINT       | Number of times buffer was read                                |
| ROWS\_PROCESSED            | BIGINT       | Total number of rows returned by the SQL statement             |
| SERIALIZABLE\_ABORTS       | BIGINT       | Number of times the transaction failed to serialize            |
| FETCHES                    | BIGINT       | Number of fetches for the SQL statement                        |
| EXECUTIONS                 | BIGINT       | Number of executions after being loaded into buffer            |
| END\_OF\_FETCH\_COUNT      | BIGINT       | Number of times the cursor executed completely after being brought to the buffer |
| LOADS                      | BIGINT       | Number of times the SQL entered the SQL pool           |
| VERSION\_COUNT             | INTEGER      | Number of versions of the execution plan                       |
| INVALIDATIONS               | BIGINT       | Number of times the execution plan became invalid              |
| PX\_SERVERS\_EXECUTIONS     | BIGINT       | Total number of executions performed in parallel               |
| CPU\_TIME                  | BIGINT       | CPU usage time for parsing/execution/fetching, etc. (unit: microseconds) |
| ELAPSED\_TIME              | BIGINT       | Time consumed for parsing/execution/fetching, etc. (unit: microseconds) |
| APPLICATION\_WAIT\_TIME    | BIGINT       | Application wait time (unit: microseconds)                    |
| CONCURRENCY\_WAIT\_TIME    | BIGINT       | Concurrency wait time (unit: microseconds)                    |
| CLUSTER\_WAIT\_TIME        | BIGINT       | Cluster wait time (reserved field) (unit: microseconds)       |
| USER\_IO\_WAIT\_TIME       | BIGINT       | User I/O wait time (unit: microseconds)                       |
| PLSQL\_EXEC\_TIME          | BIGINT       | PL execution time (unit: microseconds), reserved field        |
| JAVA\_EXEC\_TIME           | BIGINT       | Reserved field                                                 |
| SORTS                      | BIGINT       | Number of completed sorts                                      |
| SHARABLE\_MEM              | INTEGER      | Size of shared memory occupied in the SQL pool (unit: bytes) |
| TOTAL\_SHARABLE\_MEM       | INTEGER      | Total size of shared memory occupied in the SQL pool (unit: bytes), equivalent to SHARABLE\_MEM |
| BLOCK\_RECEIVED            | BIGINT       | Number of times the latest pages were fetched from other nodes in the cluster |
| CR\_BLOCK\_RECEIVED        | BIGINT       | Number of times CR pages were fetched from other nodes in the cluster |
| LOCAL\_GRANTS              | BIGINT       | Number of times local node authorized page loads in the cluster |
| REMOTE\_GRANTS             | BIGINT       | Number of times other nodes authorized page loads in the cluster |
| LOCAL\_UPGRADES            | BIGINT       | Number of times local node authorized page lock upgrades in the cluster |
| REMOTE\_UPGRADES           | BIGINT       | Number of times other nodes upgraded page locks in the cluster |