This view displays the current SQL execution statistics (one for each SQL).

|Field |Type |Description |
| --- | --- | --- |
| SQL_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL_FULLTEXT | CLOB | The full SQL text in CLOB format |
| SQL_ID | VARCHAR(13) | The unique ID value that identifies a SQL statement, obtained through hashing/encrypting the SQL text |
| SHARABLE_MEM | INTEGER | The size of shared memory used in the SQL buffer pool (in bytes) |
| PERSISTENT_MEM | INTEGER | The size of page memory occupied in the SQL buffer pool minus the unused memory at the end |
| RUNTIME_MEM | INTEGER | The memory allocated for this SQL during execution |
| LOADED_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN_VERSIONS | INTEGER | Indicates whether the sub-cursor is locked |
| USERS_OPENING | INTEGER | The number of users that have opened any sub-cursor |
| USERS_EXECUTING | INTEGER | The number of users that are executing any sub-cursor |
| SORTS | BIGINT | The number of sorts completed |
| FETCHES | BIGINT | The number of fetches for the SQL statement |
| EXECUTIONS | BIGINT | The number of executions after being loaded into the buffer pool |
| PX_SERVERS_EXECUTIONS | BIGINT | The total number of executions performed in parallel (reserved field) |
| END_OF_FETCH_COUNT | BIGINT | The number of times the cursor has completely executed after being brought to the buffer |
| ROWS_PROCESSED | BIGINT | The total number of rows returned by the SQL statement |
| SERIALIZABLE_ABORTS | BIGINT | The number of times the transaction failed to serialize |
| LOADS | BIGINT | The number of times SQL has entered the SQL buffer pool |
| INVALIDATIONS | BIGINT | The number of times the SQL has experienced object data dictionary invalidation |
| PARSE_CALLS | BIGINT | The number of parse calls |
| DISK_READS | BIGINT | The number of disk reads |
| DIRECT_WRITES | BIGINT | The number of direct writes by the cursor (reserved field) |
| DIRECT_READS | BIGINT | The number of direct reads by the cursor (reserved field) |
| BUFFER_GETS | BIGINT | The number of buffer gets |
| IO_INTERCONNECT_BYTES | BIGINT | The number of I/O bytes exchanged between the database and storage system |
| PHYSICAL_READ_REQUESTS | BIGINT | The number of physical read I/O requests initiated by the SQL |
| PHYSICAL_READ_BYTES | BIGINT | The number of bytes read from disk by the SQL |
| PHYSICAL_WRITE_REQUESTS | BIGINT | The number of physical write I/O requests initiated by the SQL |
| PHYSICAL_WRITE_BYTES | BIGINT | The number of bytes written to disk by the SQL |
| IM_SCANS | BIGINT | The number of scans in the in-memory column store (reserved field) |
| IM_SCAN_BYTES_UNCOMPRESSED | BIGINT | The number of uncompressed bytes scanned in the in-memory column store (reserved field) |
| IM_SCAN_BYTES_INMEMORY | BIGINT | The number of bytes scanned in the in-memory column store (reserved field) |
| APPLICATION_WAIT_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY_WAIT_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER_WAIT_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER_IO_WAIT_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL_EXEC_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU_TIME | BIGINT | The CPU usage time for parsing/executing/fetching (in microseconds) |
| ELAPSED_TIME | BIGINT | The elapsed time for parsing/executing/fetching (in microseconds) |
| COMMAND_TYPE | INTEGER | The command type of the SQL<br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE | VARCHAR(10) | The optimizer model of the SQL statement (reserved field) |
| OPTIMIZER_COST | BIGINT | The cost of the current query provided by the optimizer |
| OPTIMIZER_ENV | RAW(2000) | Reserved field |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT | Reserved field |
| PARSING_USER_ID | INTEGER | The user ID of the first parser of the SQL |
| PARSING_SCHEMA_ID | INTEGER | The schema ID used when parsing the SQL |
| PARSING_SCHEMA_NAME | VARCHAR(64) | The schema name used when parsing the SQL |
| ADDRESS | RAW(8) | The address of the SQL |
| HASH_VALUE | BIGINT | The hash value of the SQL, calculated from the SQL text |
| PLAN_HASH_VALUE | BIGINT | A unique identifier for an execution plan |
| MODULE | VARCHAR(64) | Reserved field |
| MODULE_HASH | BIGINT | Reserved field |
| ACTION | VARCHAR(64) | Reserved field |
| ACTION_HASH | BIGINT | Reserved field |
| OUTLINE_CATEGORY | VARCHAR(64) | If an outline is applied, this field indicates the category of the outline; otherwise, it is NULL |
| OUTLINE_SID | INTEGER | This field indicates whether the plan is using a public outline (NULL) or a private outline (corresponding session SID) |
| CHILD_ADDRESS | RAW(8) | The child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL_HASH_VALUE | BIGINT | Reserved field |
| FIRST_LOAD_TIME | DATE | The time when the SQL first entered the SQL buffer pool |
| LAST_LOAD_TIME | DATE | The time when the execution plan was loaded into the database's high-speed buffer |
| LAST_ACTIVE_TIME | DATE | The time when the SQL was last accessed |
| IS_OBSOLETE | VARCHAR(1) | Indicates whether the cursor is obsolete (Y/N) when the number of child cursors is too many |
| IS_BIND_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bind variable calculations |
| BIND_DATA | RAW(2000) | Reserved field |
| LOCKED_TOTAL | BIGINT | The total number of times the child cursor has been locked (reserved field) |
| PINNED_TOTAL | BIGINT | The total number of times the child cursor has been pinned |
| IS_REOPTIMIZABLE | VARCHAR(1) | Indicates that the actual number of rows during SQL execution differs greatly from the CBO estimate, requiring re-parsing during the next execution |
| CHILD_NUMBER | INTEGER | The child cursor number |
| RESTART_STATEMENTS | BIGINT | The number of statement restarts |
| BLOCK_RECEIVED | BIGINT | The number of times the latest pages were fetched from other nodes in the cluster |
| CR_BLOCK_RECEIVED | BIGINT | The number of times CR pages were fetched from other nodes in the cluster |
| LOCAL_GRANTS | BIGINT | The number of times this node granted loading pages in the cluster |
| REMOTE_GRANTS | BIGINT | The number of times other nodes granted loading pages in the cluster |
| LOCAL_UPGRADES | BIGINT | The number of times this node upgraded page locks in the cluster |
| REMOTE_UPGRADES | BIGINT | The number of times other nodes upgraded page locks in the cluster |
| COMPAT_MODE | INTEGER | The compatibility mode of the current SQL |
| COMPAT_ATTRS | BIGINT | The attributes of the current SQL compatibility mode |