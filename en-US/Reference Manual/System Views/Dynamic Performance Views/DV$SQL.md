This view displays the SQL execution statistics for all nodes in an ISC distributed cluster. The same CN executing the same SQL shows one entry, while different CNs executing the same SQL show multiple entries.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| SQL\_TEXT | VARCHAR(1000) | The first 1000 characters of the SQL text |
| SQL\_FULLTEXT | CLOB | Full SQL text in CLOB format |
| SQL\_ID | VARCHAR(13) | Unique identifier for a SQL statement, derived from hashing/encryption of the SQL text |
| SHARABLE\_MEM | INTEGER | Size of shared memory used in the SQL pool (in bytes) |
| PERSISTENT\_MEM | INTEGER | Size of page memory used in the SQL pool minus the unused tail memory (in bytes) |
| RUNTIME\_MEM | INTEGER | Memory requested by this SQL during execution (in bytes) |
| LOADED\_VERSIONS | INTEGER | Indicates whether the context heap is loaded |
| OPEN\_VERSIONS | INTEGER | Indicates whether sub-cursors are locked |
| USERS\_OPENING | INTEGER | Number of users opening any sub-cursor |
| USERS\_EXECUTING | INTEGER | Number of users executing any sub-cursor |
| SORTS | BIGINT | Number of completed sorts |
| FETCHES | BIGINT | Number of fetches for the SQL statement |
| EXECUTIONS | BIGINT | Number of executions after being loaded into the buffer pool |
| PX\_SERVERS\_EXECUTIONS | BIGINT | Total number of executions in parallel |
| END\_OF\_FETCH\_COUNT | BIGINT | Number of times the cursor has fully executed after being brought to the library buffer |
| ROW\_PROCESSED | BIGINT | Total number of rows returned by the SQL statement |
| SERIALIZABLE\_ABORTS | BIGINT | Number of times transactions failed to serialize |
| LOADS | BIGINT | Number of times SQL entered the SQL pool |
| INVALIDATIONS | BIGINT | Number of times the SQL experienced object data dictionary invalidation |
| PARSE\_CALLS | BIGINT | Number of parse calls |
| DISK\_READS | BIGINT | Number of disk reads |
| DIRECT\_WRITES | BIGINT | Number of direct writes by the cursor |
| DIRECT\_READS | BIGINT | Number of direct reads by the cursor |
| BUFFER\_GETS | BIGINT | Number of reads from the buffer |
| IO\_INTERCONNECT\_BYTES | BIGINT | Number of I/O bytes exchanged between the database and storage system |
| PHYSICAL\_READ\_REQUESTS | BIGINT | Number of physical read I/O requests issued by SQL |
| PHYSICAL\_READ\_BYTES | BIGINT | Number of bytes read from disk by SQL |
| PHYSICAL\_WRITE\_REQUESTS | BIGINT | Number of physical write I/O requests issued by SQL |
| PHYSICAL\_WRITE\_BYTES | BIGINT | Number of bytes written to disk by SQL |
| IM\_SCANS | BIGINT | Number of in-memory column store segment scans |
| IM\_SCAN\_BYTES\_UNCOMPRESSED | BIGINT | Uncompressed bytes of in-memory column store segment scans |
| IM\_SCAN\_BYTES\_INMEMORY | BIGINT | Bytes of in-memory column store segment scans |
| APPLICATION\_WAIT\_TIME | BIGINT | Application wait time (in microseconds) |
| CONCURRENCY\_WAIT\_TIME | BIGINT | Concurrency wait time (in microseconds) |
| CLUSTER\_WAIT\_TIME | BIGINT | Cluster wait time (reserved field) (in microseconds) |
| USER\_IO\_WAIT\_TIME | BIGINT | User I/O wait time (in microseconds) |
| PLSQL\_EXEC\_TIME | BIGINT | PL execution time (in microseconds), reserved field |
| CPU\_TIME | BIGINT | CPU usage time for parsing/execution/fetching (in microseconds) |
| ELAPSED\_TIME | BIGINT | Time consumption for parsing/execution/fetching (in microseconds) |
| COMMAND\_TYPE | INTEGER | Command type of the SQL<br>\* 1: SQL\_QUERY<br>\* 2: SQL\_INSERT<br>\* 3: SQL\_UPDATE<br>\* 4: SQL\_DELETE<br>\* 5: SQL\_MERGE<br>\* 6: SQL\_WITH<br>\* 7: SQL\_ANONYMOUS\_BLOCK |
| OPTIMIZER\_MODE | VARCHAR(10) | Optimizer model of the SQL statement |
| OPTIMIZER\_COST | BIGINT | Cost of the current query provided by the optimizer |
| OPTIMIZER\_ENV | RAW(2000) | Reserved field |
| OPTIMIZER\_ENV\_HASH\_VALUE | BIGINT | Reserved field |
| PARSING\_USER\_ID | INTEGER | User ID of the first parser of the SQL |
| PARSING\_SCHEMA\_ID | INTEGER | Schema ID used when parsing the SQL |
| PARSING\_SCHEMA\_NAME | VARCHAR(64) | Schema name used when parsing the SQL |
| ADDRESS | RAW(8) | SQL address |
| HASH\_VALUE | BIGINT | Hash value of the SQL computed from the SQL text |
| PLAN\_HASH\_VALUE | BIGINT | Unique identifier for an execution plan |
| MODULE | VARCHAR(64) | Reserved field |
| MODULE\_HASH | BIGINT | Reserved field |
| ACTION | VARCHAR(64) | Reserved field |
| ACTION\_HASH | BIGINT | Reserved field |
| OUTLINE\_CATEGORY | VARCHAR(64) | The category of the outline if an outline is applied, otherwise NULL |
| OUTLINE\_SID | INTEGER | Determines whether the plan uses a public outline (if NULL) or a private outline (if the field contains the session SID) |
| CHILD\_ADDRESS | RAW(8) | Child cursor address |
| SQLTYPE | INTEGER | Reserved field |
| LITERAL\_HASH\_VALUE | BIGINT | Reserved field |
| FIRST\_LOAD\_TIME | DATE | Time when SQL first entered the SQL pool |
| LAST\_LOAD\_TIME | DATE | Time when the query plan was loaded into the library high-speed buffer |
| LAST\_ACTIVE\_TIME | DATE | Time of the last access to this SQL |
| IS\_OBSOLETE | VARCHAR(1) | Indicates whether the cursor is obsolete (Y/N) when the number of sub-cursors is too high |
| IS\_BIND\_SENSITIVE | VARCHAR(1) | Indicates whether the SQL statement is suitable for bound computation |
| BIND\_DATA | RAW(2000) | Reserved field |
| LOCKED\_TOTAL | BIGINT | Total number of times the sub-cursor was locked, reserved field |
| PINNED\_TOTAL | BIGINT | Total number of times the sub-cursor was pinned |
| IS\_REOPTIMIZABLE | VARCHAR(1) | Indicates if the actual number of rows at execution significantly differs from the CBO estimated number, requiring re-parsing on the next execution |
| CHILD\_NUMBER | INTEGER | Child cursor number |
| RESTART\_STATEMENTS | BIGINT | Number of statement restarts |