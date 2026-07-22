This view displays the current SQL execution statistics for all SQL statements (one per SQL).

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| SQL\_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL\_FULLTEXT | CLOB | The full SQL in CLOB format |
| SQL\_ID | VARCHAR(13) | The ID value that uniquely identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| SHARABLE\_MEM | INTEGER | The size of shared memory used in the SQL pool (in bytes) |
| PERSISTENT\_MEM | INTEGER | Memory used in the SQL pool minus the unused memory at the end |
| RUNTIME\_MEM | INTEGER | Memory allocated during the execution of this SQL |
| LOADED\_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN\_VERSIONS | INTEGER | Indicates whether the sub-cursors are locked |
| USERS\_OPENING | INTEGER | The number of users opening any sub-cursor |
| USERS\_EXECUTING | INTEGER | The number of users executing any sub-cursor |
| SORTS | BIGINT | The number of completed sorts |
| FETCHES | BIGINT | The fetch count for the SQL statement |
| EXECUTIONS | BIGINT | The number of executions after loading into the buffer library |
| PX\_SERVERS\_EXECUTIONS | BIGINT | The total number of executions performed in parallel (reserved field) |
| END\_OF\_FETCH\_COUNT | BIGINT | The number of times the cursor was completely executed after being brought into the library buffer |
| ROWS\_PROCESSED | BIGINT | The total number of rows returned by the SQL statement |
| SERIALIZABLE\_ABORTS | BIGINT | The number of times the transaction failed to serialize |
| LOADS | BIGINT | The number of times SQL entered the SQL pool |
| INVALIDATIONS | BIGINT | The number of times SQL encountered object data dictionary invalidations |
| PARSE\_CALLS | BIGINT | The number of parse calls |
| DISK\_READS | BIGINT | The number of disk reads |
| DIRECT\_WRITES | BIGINT | The number of times the cursor wrote directly (reserved field) |
| DIRECT\_READS | BIGINT | The number of times the cursor read directly (reserved field) |
| BUFFER\_GETS | BIGINT | The number of reads from the buffer |
| IO\_INTERCONNECT\_BYTES | BIGINT | The number of I/O bytes exchanged between the database and storage system |
| PHYSICAL\_READ\_REQUESTS | BIGINT | The number of physical read I/O requests issued by SQL |
| PHYSICAL\_READ\_BYTES | BIGINT | The number of bytes read from disk by SQL |
| PHYSICAL\_WRITE\_REQUESTS | BIGINT | The number of physical write I/O requests issued by SQL (SQL execution does not write to disk immediately; the dbwr thread performs the write, so value changes for this statistic cannot be observed at the SQL level. It is recommended to observe from V$SYSSTAT/V$SESSTAT/V$MYSTAT views)  |
| PHYSICAL\_WRITE\_BYTES | BIGINT | The number of bytes written to disk by SQL (SQL execution does not write to disk immediately; the dbwr thread performs the write, so value changes for this statistic cannot be observed at the SQL level. It is recommended to observe from V$SYSSTAT/V$SESSTAT/V$MYSTAT views)  |
| IM\_SCANS | BIGINT | The number of scans on in-memory column storage segments (reserved field) |
| IM\_SCAN\_BYTES\_UNCOMPRESSED | BIGINT | The number of uncompressed bytes scanned from in-memory column storage segments (reserved field) |
| IM\_SCAN\_BYTES\_INMEMORY | BIGINT | The number of bytes scanned from in-memory column storage segments (reserved field) |
| APPLICATION\_WAIT\_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY\_WAIT\_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER\_WAIT\_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER\_IO\_WAIT\_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL\_EXEC\_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU\_TIME | BIGINT | CPU time used for parsing/executing/fetching, etc. (in microseconds) |
| ELAPSED\_TIME | BIGINT | Time consumed for parsing/executing/fetching, etc. (in microseconds) |
| COMMAND\_TYPE | INTEGER | Command type of SQL<br/>* 1: SQL\_QUERY  <br/>* 2: SQL\_INSERT  <br/>* 3: SQL\_UPDATE  <br/>* 4: SQL\_DELETE  <br/>* 5: SQL\_MERGE  <br/>* 6: SQL\_WITH  <br/>* 7: SQL\_ANONYMOUS\_BLOCK |
| OPTIMIZER\_MODE | VARCHAR(10) | The optimizer model of the SQL statement (reserved field) |
| OPTIMIZER\_COST | BIGINT | The cost of the current query estimated by the optimizer |
| OPTIMIZER\_ENV | RAW(2000) | Reserved field |
| OPTIMIZER\_ENV\_HASH\_VALUE | BIGINT | Reserved field |
| PARSING\_USER\_ID | INTEGER | The user ID of the first user to parse this SQL |
| PARSING\_SCHEMA\_ID | INTEGER | The schema ID used to parse this SQL |
| PARSING\_SCHEMA\_NAME | VARCHAR(64) | The name of the schema used to parse this SQL |
| ADDRESS | RAW(8) | SQL address |
| HASH\_VALUE | BIGINT | The hash value of the SQL, calculated from the SQL text |
| PLAN\_HASH\_VALUE | BIGINT | A unique identifier for an execution plan |
| MODULE | VARCHAR(64) | Reserved field |
| MODULE\_HASH | BIGINT | Reserved field |
| ACTION | VARCHAR(64) | Reserved field |
| ACTION\_HASH | BIGINT | Reserved field |
| OUTLINE\_CATEGORY | VARCHAR(64) | The category of the outline if one was applied, otherwise NULL |
| OUTLINE\_SID | INTEGER | This field can determine if the plan uses a public outline (NULL) or a private outline (the corresponding session SID) |
| CHILD\_ADDRESS | RAW(8) | Child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL\_HASH\_VALUE | BIGINT | Reserved field |
| FIRST\_LOAD\_TIME | DATE | The time when SQL first entered the SQL pool |
| LAST\_LOAD\_TIME | DATE | The time when the query plan was loaded into the library high-speed buffer |
| LAST\_ACTIVE\_TIME | DATE | The last time this SQL was accessed |
| IS\_OBSOLETE | VARCHAR(1) | Indicates whether the cursor has become obsolete (Y/N) when the number of child cursors is too high |
| IS\_BIND\_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bind variables |
| BIND\_DATA | RAW(2000) | Reserved field |
| LOCKED\_TOTAL | BIGINT | The total number of times child cursors were locked (reserved field) |
| PINNED\_TOTAL | BIGINT | The total number of times child cursors were pinned |
| IS\_REOPTIMIZABLE | VARCHAR(1) | Indicates that the actual row count during execution differs greatly from the CBO estimated row count, needing re-parsing on next execution |
| CHILD\_NUMBER | INTEGER | Child cursor number |
| RESTART\_STATEMENTS | BIGINT | The number of statement restarts |
| BLOCK\_RECEIVED | BIGINT | The number of times the latest pages were received from other nodes in the cluster |
| CR\_BLOCK\_RECEIVED | BIGINT | The number of CR pages received from other nodes in the cluster |
| LOCAL\_GRANTS | BIGINT | The number of times local node authorized loading of pages in the cluster |
| REMOTE\_GRANTS | BIGINT | The number of times other nodes authorized loading of pages in the cluster |
| LOCAL\_UPGRADES | BIGINT | The number of times local node authorized page lock upgrades in the cluster |
| REMOTE\_UPGRADES | BIGINT | The number of times other nodes authorized page lock upgrades in the cluster |