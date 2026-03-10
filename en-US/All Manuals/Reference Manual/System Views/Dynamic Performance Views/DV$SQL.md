This view displays the SQL execution statistics for all nodes in an ISC distributed cluster. The same CN executing the same SQL shows one entry, while different CNs executing the same SQL show multiple entries.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| SQL_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL_FULLTEXT | CLOB | Full SQL text in CLOB format |
| SQL_ID | VARCHAR(13) | Unique identifier for a SQL statement, derived from hashing/encryption of the SQL text |
| SHARABLE_MEM | INTEGER | Size of shared memory used in the SQL buffer pool (in bytes) |
| PERSISTENT_MEM | INTEGER | Size of page memory used in the SQL buffer pool minus the unused tail memory (in bytes) |
| RUNTIME_MEM | INTEGER | Memory requested by this SQL during execution (in bytes) |
| LOADED_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN_VERSIONS | INTEGER | Indicates whether sub-cursors are locked |
| USERS_OPENING | INTEGER | Number of users opening any sub-cursor |
| USERS_EXECUTING | INTEGER | Number of users executing any sub-cursor |
| SORTS | BIGINT | Number of completed sorts |
| FETCHES | BIGINT | Number of fetches for the SQL statement |
| EXECUTIONS | BIGINT | Number of executions after being loaded into the buffer pool |
| PX_SERVERS_EXECUTIONS | BIGINT | Total number of executions in parallel |
| END_OF_FETCH_COUNT | BIGINT | Number of times the cursor has fully executed after being brought to the library buffer |
| ROW_PROCESSED | BIGINT | Total number of rows returned by the SQL statement |
| SERIALIZABLE_ABORTS | BIGINT | Number of times transactions failed to serialize |
| LOADS | BIGINT | Number of times SQL entered the SQL buffer pool |
| INVALIDATIONS | BIGINT | Number of times the SQL experienced object data dictionary invalidation |
| PARSE_CALLS | BIGINT | Number of parse calls |
| DISK_READS | BIGINT | Number of disk reads |
| DIRECT_WRITES | BIGINT | Number of direct writes by the cursor |
| DIRECT_READS | BIGINT | Number of direct reads by the cursor |
| BUFFER_GETS | BIGINT | Number of reads from the buffer |
| IO_INTERCONNECT_BYTES | BIGINT | Number of I/O bytes exchanged between the database and storage system |
| PHYSICAL_READ_REQUESTS | BIGINT | Number of physical read I/O requests issued by SQL |
| PHYSICAL_READ_BYTES | BIGINT | Number of bytes read from disk by SQL |
| PHYSICAL_WRITE_REQUESTS | BIGINT | Number of physical write I/O requests issued by SQL |
| PHYSICAL_WRITE_BYTES | BIGINT | Number of bytes written to disk by SQL |
| IM_SCANS | BIGINT | Number of in-memory column store segment scans |
| IM_SCAN_BYTES_UNCOMPRESSED | BIGINT | Uncompressed bytes of in-memory column store segment scans |
| IM_SCAN_BYTES_INMEMORY | BIGINT | Bytes of in-memory column store segment scans |
| APPLICATION_WAIT_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY_WAIT_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER_WAIT_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER_IO_WAIT_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL_EXEC_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU_TIME | BIGINT | CPU usage time for parsing/execution/fetching (in microseconds) |
| ELAPSED_TIME | BIGINT | Time consumption for parsing/execution/fetching (in microseconds) |
| COMMAND_TYPE | INTEGER | Command type of the SQL<br>\* 1: SQL_QUERY<br>\* 2: SQL_INSERT<br>\* 3: SQL_UPDATE<br>\* 4: SQL_DELETE<br>\* 5: SQL_MERGE<br>\* 6: SQL_WITH<br>\* 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE | VARCHAR(10) | Optimizer model of the SQL statement |
| OPTIMIZER_COST | BIGINT | Cost of the current query provided by the optimizer |
| OPTIMIZER_ENV | RAW(2000) | Reserved field |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT | Reserved field |
| PARSING_USER_ID | INTEGER | User ID of the first parser of the SQL |
| PARSING_SCHEMA_ID | INTEGER | Schema ID used when parsing the SQL |
| PARSING_SCHEMA_NAME | VARCHAR(64) | Schema name used when parsing the SQL |
| ADDRESS | RAW(8) | SQL address |
| HASH_VALUE | BIGINT | Hash value of the SQL computed from the SQL text |
| PLAN_HASH_VALUE | BIGINT | Unique identifier for an execution plan |
| MODULE | VARCHAR(64) | Reserved field |
| MODULE_HASH | BIGINT | Reserved field |
| ACTION | VARCHAR(64) | Reserved field |
| ACTION_HASH | BIGINT | Reserved field |
| OUTLINE_CATEGORY | VARCHAR(64) | The category of the outline if an outline is applied, otherwise NULL |
| OUTLINE_SID | INTEGER | Determines whether the plan uses a public outline (if NULL) or a private outline (if the field contains the session SID) |
| CHILD_ADDRESS | RAW(8) | Child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL_HASH_VALUE | BIGINT | Reserved field |
| FIRST_LOAD_TIME | DATE | Time when SQL first entered the SQL buffer pool |
| LAST_LOAD_TIME | DATE | Time when the query plan was loaded into the library high-speed buffer |
| LAST_ACTIVE_TIME | DATE | Time of the last access to this SQL |
| IS_OBSOLETE | VARCHAR(1) | Indicates whether the cursor is obsolete (Y/N) when the number of sub-cursors is too high |
| IS_BIND_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bound computation |
| BIND_DATA | RAW(2000) | Reserved field |
| LOCKED_TOTAL | BIGINT | Total number of times the sub-cursor was locked, reserved field |
| PINNED_TOTAL | BIGINT | Total number of times the sub-cursor was pinned |
| IS_REOPTIMIZABLE | VARCHAR(1) | Indicates if the actual number of rows at execution significantly differs from the CBO estimated number, requiring re-parsing on the next execution |
| CHILD_NUMBER | INTEGER | Child cursor number |
| RESTART_STATEMENTS | BIGINT | Number of statement restarts |