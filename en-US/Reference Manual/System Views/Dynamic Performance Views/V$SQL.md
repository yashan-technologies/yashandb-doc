This view displays the current SQL execution statistics (one for each SQL).

|Field |Type |Description |
| --- | --- | --- |
| SQL\_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL\_FULLTEXT | CLOB | The full SQL text in CLOB format |
| SQL\_ID | VARCHAR(13) | The unique ID value that identifies a SQL statement, obtained through hashing/encrypting the SQL text |
| SHARABLE\_MEM | INTEGER | The size of shared memory used in the SQL pool (in bytes) |
| PERSISTENT\_MEM | INTEGER | The size of page memory occupied in the SQL pool minus the unused memory at the end |
| RUNTIME\_MEM | INTEGER | The memory allocated for this SQL during execution (reserved field) |
| LOADED\_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN\_VERSIONS | INTEGER | Indicates whether the sub-cursor is locked |
| USERS\_OPENING | INTEGER | The number of users that have opened any sub-cursor |
| USERS\_EXECUTING | INTEGER | The number of users that are executing any sub-cursor |
| SORTS | BIGINT | The number of sorts completed |
| FETCHES | BIGINT | The number of fetches for the SQL statement |
| EXECUTIONS | BIGINT | The number of executions after being loaded into the buffer pool |
| PX\_SERVERS\_EXECUTIONS | BIGINT | The total number of executions performed in parallel (reserved field) |
| END\_OF\_FETCH\_COUNT | BIGINT | The number of times the cursor has completely executed after being brought to the buffer |
| ROWS\_PROCESSED | BIGINT | The total number of rows returned by the SQL statement |
| SERIALIZABLE\_ABORTS | BIGINT | The number of times the transaction failed to serialize |
| LOADS | BIGINT | The number of times SQL has entered the SQL pool |
| INVALIDATIONS | BIGINT | The number of times the SQL has experienced object data dictionary invalidation |
| PARSE\_CALLS | BIGINT | The number of parse calls |
| DISK\_READS | BIGINT | The number of disk reads |
| DIRECT\_WRITES | BIGINT | The number of direct writes by the cursor (reserved field) |
| DIRECT\_READS | BIGINT | The number of direct reads by the cursor (reserved field) |
| BUFFER\_GETS | BIGINT | The number of buffer gets |
| IO\_INTERCONNECT\_BYTES | BIGINT | The number of I/O bytes exchanged between the database and storage system |
| PHYSICAL\_READ\_REQUESTS | BIGINT | The number of physical read I/O requests initiated by the SQL |
| PHYSICAL\_READ\_BYTES | BIGINT | The number of bytes read from disk by the SQL |
| PHYSICAL\_WRITE\_REQUESTS | BIGINT | The number of physical write I/O requests initiated by the SQL |
| PHYSICAL\_WRITE\_BYTES | BIGINT | The number of bytes written to disk by the SQL |
| IM\_SCANS | BIGINT | The number of scans in the in-memory column store (reserved field) |
| IM\_SCAN\_BYTES\_UNCOMPRESSED | BIGINT | The number of uncompressed bytes scanned in the in-memory column store (reserved field) |
| IM\_SCAN\_BYTES\_INMEMORY | BIGINT | The number of bytes scanned in the in-memory column store (reserved field) |
| APPLICATION\_WAIT\_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY\_WAIT\_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER\_WAIT\_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER\_IO\_WAIT\_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL\_EXEC\_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU\_TIME | BIGINT | The CPU usage time for parsing/executing/fetching (in microseconds) |
| ELAPSED\_TIME | BIGINT | The elapsed time for parsing/executing/fetching (in microseconds) |
| COMMAND\_TYPE | INTEGER | The command type of the SQL<br/>* 1: SQL\_QUERY  <br/>* 2: SQL\_INSERT  <br/>* 3: SQL\_UPDATE  <br/>* 4: SQL\_DELETE  <br/>* 5: SQL\_MERGE  <br/>* 6: SQL\_WITH  <br/>* 7: SQL\_ANONYMOUS\_BLOCK |
| OPTIMIZER\_MODE | VARCHAR(10) | The optimizer model of the SQL statement (reserved field) |
| OPTIMIZER\_COST | BIGINT | The cost of the current query provided by the optimizer |
| OPTIMIZER\_ENV | RAW(2000) | Reserved field |
| OPTIMIZER\_ENV\_HASH\_VALUE | BIGINT | Reserved field |
| PARSING\_USER\_ID | INTEGER | The user ID of the first parser of the SQL |
| PARSING\_SCHEMA\_ID | INTEGER | The schema ID used when parsing the SQL |
| PARSING\_SCHEMA\_NAME | VARCHAR(64) | The schema name used when parsing the SQL |
| ADDRESS | RAW(8) | The address of the SQL |
| HASH\_VALUE | BIGINT | The hash value of the SQL, calculated from the SQL text |
| PLAN\_HASH\_VALUE | BIGINT | A unique identifier for an execution plan |
| MODULE                   | VARCHAR(64) |  The name of the module in which the SQL statement is currently executing. This module name is set by calling DBMS_APPLICATION_INFO.SET_MODULE                                          |
| MODULE_HASH              | BIGINT     |   The hash value of the module name                                          |
| ACTION                   | VARCHAR(64) |   The name of the action being executed when the SQL statement is first parsed. This action name is set by invoking DBMS_APPLICATION_INFO.SET_ACTION                                           |
| ACTION_HASH              | BIGINT     |   Hash value of the operation name                                           |
| OUTLINE\_CATEGORY | VARCHAR(64) | If an outline is applied, this field indicates the category of the outline; otherwise, it is NULL |
| OUTLINE\_SID | INTEGER | This field indicates whether the plan is using a public outline (NULL) or a private outline (corresponding session SID) |
| CHILD\_ADDRESS | RAW(8) | The child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL\_HASH\_VALUE | BIGINT | Reserved field |
| FIRST\_LOAD\_TIME | DATE | The time when the SQL first entered the SQL pool |
| LAST\_LOAD\_TIME | DATE | The time when the execution plan was loaded into the database's high-speed buffer |
| LAST\_ACTIVE\_TIME | DATE | The time when the SQL was last accessed |
| IS\_OBSOLETE | VARCHAR(1) | Indicates whether the cursor is obsolete (Y/N) when the number of child cursors is too many |
| IS\_BIND\_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bind variable calculations |
| BIND\_DATA | RAW(2000) | Reserved field |
| LOCKED\_TOTAL | BIGINT | The total number of times the child cursor has been locked (reserved field) |
| PINNED\_TOTAL | BIGINT | The total number of times the child cursor has been pinned |
| IS\_REOPTIMIZABLE | VARCHAR(1) | Indicates that the actual number of rows during SQL execution differs greatly from the CBO estimate, requiring re-parsing during the next execution |
| CHILD\_NUMBER | INTEGER | The child cursor number |
| RESTART\_STATEMENTS | BIGINT | The number of statement restarts |
| BLOCK\_RECEIVED | BIGINT | The number of times the latest pages were fetched from other nodes in the cluster |
| CR\_BLOCK\_RECEIVED | BIGINT | The number of times CR pages were fetched from other nodes in the cluster |
| LOCAL\_GRANTS | BIGINT | The number of times this node granted loading pages in the cluster |
| REMOTE\_GRANTS | BIGINT | The number of times other nodes granted loading pages in the cluster |
| LOCAL\_UPGRADES | BIGINT | The number of times this node upgraded page locks in the cluster |
| REMOTE\_UPGRADES | BIGINT | The number of times other nodes upgraded page locks in the cluster |
| COMPAT\_MODE | INTEGER | The compatibility mode of the current SQL |
| COMPAT\_ATTRS | BIGINT | The attributes of the current SQL compatibility mode |