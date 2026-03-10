This view displays the current SQL execution statistics for all SQL statements (one per SQL).

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| SQL_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL_FULLTEXT | CLOB | The full SQL in CLOB format |
| SQL_ID | VARCHAR(13) | The ID value that uniquely identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| SHARABLE_MEM | INTEGER | The size of shared memory used in the SQL buffer pool (in bytes) |
| PERSISTENT_MEM | INTEGER | Memory used in the SQL buffer pool minus the unused memory at the end |
| RUNTIME_MEM | INTEGER | Memory allocated during the execution of this SQL |
| LOADED_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN_VERSIONS | INTEGER | Indicates whether the sub-cursors are locked |
| USERS_OPENING | INTEGER | The number of users opening any sub-cursor |
| USERS_EXECUTING | INTEGER | The number of users executing any sub-cursor |
| SORTS | BIGINT | The number of completed sorts |
| FETCHES | BIGINT | The fetch count for the SQL statement |
| EXECUTIONS | BIGINT | The number of executions after loading into the buffer library |
| PX_SERVERS_EXECUTIONS | BIGINT | The total number of executions performed in parallel (reserved field) |
| END_OF_FETCH_COUNT | BIGINT | The number of times the cursor was completely executed after being brought into the library buffer |
| ROWS_PROCESSED | BIGINT | The total number of rows returned by the SQL statement |
| SERIALIZABLE_ABORTS | BIGINT | The number of times the transaction failed to serialize |
| LOADS | BIGINT | The number of times SQL entered the SQL buffer pool |
| INVALIDATIONS | BIGINT | The number of times SQL encountered object data dictionary invalidations |
| PARSE_CALLS | BIGINT | The number of parse calls |
| DISK_READS | BIGINT | The number of disk reads |
| DIRECT_WRITES | BIGINT | The number of times the cursor wrote directly (reserved field) |
| DIRECT_READS | BIGINT | The number of times the cursor read directly (reserved field) |
| BUFFER_GETS | BIGINT | The number of reads from the buffer |
| IO_INTERCONNECT_BYTES | BIGINT | The number of I/O bytes exchanged between the database and storage system |
| PHYSICAL_READ_REQUESTS | BIGINT | The number of physical read I/O requests issued by SQL |
| PHYSICAL_READ_BYTES | BIGINT | The number of bytes read from disk by SQL |
| PHYSICAL_WRITE_REQUESTS | BIGINT | The number of physical write I/O requests issued by SQL |
| PHYSICAL_WRITE_BYTES | BIGINT | The number of bytes written to disk by SQL |
| IM_SCANS | BIGINT | The number of scans on in-memory column storage segments (reserved field) |
| IM_SCAN_BYTES_UNCOMPRESSED | BIGINT | The number of uncompressed bytes scanned from in-memory column storage segments (reserved field) |
| IM_SCAN_BYTES_INMEMORY | BIGINT | The number of bytes scanned from in-memory column storage segments (reserved field) |
| APPLICATION_WAIT_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY_WAIT_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER_WAIT_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER_IO_WAIT_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL_EXEC_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU_TIME | BIGINT | CPU time used for parsing/executing/fetching, etc. (in microseconds) |
| ELAPSED_TIME | BIGINT | Time consumed for parsing/executing/fetching, etc. (in microseconds) |
| COMMAND_TYPE | INTEGER | Command type of SQL<br/>* 1: SQL_QUERY  <br/>* 2: SQL_INSERT  <br/>* 3: SQL_UPDATE  <br/>* 4: SQL_DELETE  <br/>* 5: SQL_MERGE  <br/>* 6: SQL_WITH  <br/>* 7: SQL_ANONYMOUS_BLOCK |
| OPTIMIZER_MODE | VARCHAR(10) | The optimizer model of the SQL statement (reserved field) |
| OPTIMIZER_COST | BIGINT | The cost of the current query estimated by the optimizer |
| OPTIMIZER_ENV | RAW(2000) | Reserved field |
| OPTIMIZER_ENV_HASH_VALUE | BIGINT | Reserved field |
| PARSING_USER_ID | INTEGER | The user ID of the first user to parse this SQL |
| PARSING_SCHEMA_ID | INTEGER | The schema ID used to parse this SQL |
| PARSING_SCHEMA_NAME | VARCHAR(64) | The name of the schema used to parse this SQL |
| ADDRESS | RAW(8) | SQL address |
| HASH_VALUE | BIGINT | The hash value of the SQL, calculated from the SQL text |
| PLAN_HASH_VALUE | BIGINT | A unique identifier for an execution plan |
| MODULE | VARCHAR(64) | Reserved field |
| MODULE_HASH | BIGINT | Reserved field |
| ACTION | VARCHAR(64) | Reserved field |
| ACTION_HASH | BIGINT | Reserved field |
| OUTLINE_CATEGORY | VARCHAR(64) | The category of the outline if one was applied, otherwise NULL |
| OUTLINE_SID | INTEGER | This field can determine if the plan uses a public outline (NULL) or a private outline (the corresponding session SID) |
| CHILD_ADDRESS | RAW(8) | Child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL_HASH_VALUE | BIGINT | Reserved field |
| FIRST_LOAD_TIME | DATE | The time when SQL first entered the SQL buffer pool |
| LAST_LOAD_TIME | DATE | The time when the query plan was loaded into the library high-speed buffer |
| LAST_ACTIVE_TIME | DATE | The last time this SQL was accessed |
| IS_OBSOLETE | VARCHAR(1) | Indicates whether the cursor has become obsolete (Y/N) when the number of child cursors is too high |
| IS_BIND_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bind variables |
| BIND_DATA | RAW(2000) | Reserved field |
| LOCKED_TOTAL | BIGINT | The total number of times child cursors were locked (reserved field) |
| PINNED_TOTAL | BIGINT | The total number of times child cursors were pinned |
| IS_REOPTIMIZABLE | VARCHAR(1) | Indicates that the actual row count during execution differs greatly from the CBO estimated row count, needing re-parsing on next execution |
| CHILD_NUMBER | INTEGER | Child cursor number |
| RESTART_STATEMENTS | BIGINT | The number of statement restarts |
| BLOCK_RECEIVED | BIGINT | The number of times the latest pages were received from other nodes in the cluster |
| CR_BLOCK_RECEIVED | BIGINT | The number of CR pages received from other nodes in the cluster |
| LOCAL_GRANTS | BIGINT | The number of times local node authorized loading of pages in the cluster |
| REMOTE_GRANTS | BIGINT | The number of times other nodes authorized loading of pages in the cluster |
| LOCAL_UPGRADES | BIGINT | The number of times local node authorized page lock upgrades in the cluster |
| REMOTE_UPGRADES | BIGINT | The number of times other nodes authorized page lock upgrades in the cluster |