This view displays the statistics of each SQL in the shared SQL area, including memory consumption, parsing, optimization, and execution information for the SQL statements.

|Field |Type |Description |
| --- | --- | --- |
| SQL\_TEXT            | VARCHAR(1000)    | First 1000 characters of the SQL text               |
| SQL\_FULLTEXT        | CLOB             | Full text of the SQL in CLOB format                  |
| SQL\_ID              | VARCHAR(13)      | Unique ID value that identifies a SQL statement, derived from the hash/encryption of the SQL text |
| SHARABLE\_MEM        | INTEGER          | Total shared memory occupied by this SQL and all version execution plans |
| PERSISTENT\_MEM      | INTEGER          | Total memory actually used by this SQL and all version execution plans |
| RUNTIME\_MEM         | INTEGER          | Total memory requested from the application heap during runtime for this SQL and all version execution plans |
| VERSION\_COUNT       | INTEGER          | Number of execution plan versions                     |
| LOADED\_VERSIONS     | INTEGER          | Number of loaded execution plan versions              |
| OPEN\_VERSIONS       | INTEGER          | Number of execution plan versions in use              |
| USERS\_OPENING       | INTEGER          | Number of users utilizing all execution plans         |
| USERS\_EXECUTING     | INTEGER          | Number of users executing all plans                   |
| SORTS                | BIGINT           | Number of sorts                                      |
| FETCHES              | BIGINT           | Number of fetches                                    |
| EXECUTIONS           | BIGINT           | Number of executions                                  |
| PX\_SERVERS\_EXECUTIONS| BIGINT          | Number of executions by the parallel execution engine (reserved field) |
| END\_OF\_FETCH\_COUNT| BIGINT           | Number of times reached the end of fetch; if currently in fetch or failed midway, statistics will not increase, thus this value should be less than or equal to the value of EXECUTIONS field |
| ROWS\_PROCESSED      | BIGINT           | Number of rows processed                              |
| SERIALIZABLE\_ABORTS | BIGINT           | Number of times transactions failed to serialize      |
| LOADS                | BIGINT           | Number of times execution plans have been loaded (reserved field) |
| INVALIDATIONS        | BIGINT           | Number of times execution plans have been invalidated  |
| PARSE\_CALLS         | BIGINT           | Number of parse calls                                 |
| DISK\_READS          | BIGINT           | Number of disk reads                                  |
| DIRECT\_WRITES       | BIGINT           | Number of direct writes                               |
| DIRECT\_READS        | BIGINT           | Number of direct reads                                |
| BUFFER\_GETS         | BIGINT           | Number of times buffer is fetched from buffer        |
| IO\_INTERCONNECT\_BYTES| BIGINT         | Number of I/O interactions between the database and storage system |
| PHYSICAL\_READ\_REQUESTS| BIGINT        | Number of physical read requests                      |
| PHYSICAL\_READ\_BYTES| BIGINT           | Number of bytes read physically                       |
| PHYSICAL\_WRITE\_REQUESTS| BIGINT       | Number of physical write requests (SQL execution does not write to disk immediately; the dbwr thread performs the write, so value changes for this statistic cannot be observed at the SQL level. It is recommended to observe from V$SYSSTAT/V$SESSTAT/V$MYSTAT views) |
| PHYSICAL\_WRITE\_BYTES| BIGINT          | Number of bytes written physically (SQL execution does not write to disk immediately; the dbwr thread performs the write, so value changes for this statistic cannot be observed at the SQL level. It is recommended to observe from V$SYSSTAT/V$SESSTAT/V$MYSTAT views) |
| APPLICATION\_WAIT\_TIME| BIGINT         | Application wait time (unit: microseconds)          |
| CONCURRENCY\_WAIT\_TIME| BIGINT         | Concurrency wait time (unit: microseconds)          |
| CLUSTER\_WAIT\_TIME  | BIGINT           | Wait time between clusters (reserved field) (unit: microseconds) |
| USER\_IO\_WAIT\_TIME | BIGINT           | User I/O wait time (unit: microseconds)             |
| PLSQL\_EXEC\_TIME    | BIGINT           | PL execution time (unit: microseconds), reserved field |
| CPU\_TIME            | BIGINT           | CPU time for parsing, executing, fetching data (unit: microseconds) |
| ELAPSED\_TIME        | BIGINT           | Time spent in parsing, executing, fetching data; includes execution time on DN side for ISC distributed clusters (unit: microseconds) |
| COMMAND\_TYPE        | INTEGER          | Command type of the SQL<br/> * 1: SQL\_QUERY  <br/> * 2: SQL\_INSERT  <br/> * 3: SQL\_UPDATE  <br/> * 4: SQL\_DELETE  <br/> * 5: SQL\_MERGE  <br/> * 6: SQL\_WITH  <br/> * 7: SQL\_ANONYMOUS\_BLOCK |
| OPTIMIZER\_MODE      | VARCHAR(10)      | Reserved field                                       |
| OPTIMIZER\_COST      | BIGINT           | Cost of the query as estimated by the optimizer      |
| OPTIMIZER\_ENV       | RAW(2000)        | Reserved field                                       |
| OPTIMIZER\_ENV\_HASH\_VALUE| BIGINT     | Reserved field                                       |
| PARSING\_USER\_ID    | INTEGER         | User ID of the first user to parse this SQL         |
| PARSING\_SCHEMA\_ID  | INTEGER         | Schema ID used to parse this SQL                     |
| PARSING\_SCHEMA\_NAME| VARCHAR(64)      | Schema name used to parse this SQL                   |
| ADDRESS              | RAW(8)           | SQL address                                          |
| HASH\_VALUE          | BIGINT           | Hash value of the SQL, calculated from the SQL text  |
| PLAN\_HASH\_VALUE    | BIGINT           | Unique identifier for an execution plan              |
| MODULE               | VARCHAR(64)      | Reserved field                                       |
| MODULE\_HASH         | BIGINT           | Reserved field                                       |
| ACTION               | VARCHAR(64)      | Reserved field                                       |
| ACTION\_HASH         | BIGINT           | Reserved field                                       |
| OUTLINE\_CATEGORY    | VARCHAR(64)      | If an outline is applied, this field shows the outline category; otherwise NULL |
| OUTLINE\_SID         | INTEGER          | This field indicates whether the plan uses a public outline (NULL) or a private outline (corresponding session SID) |
| LAST\_ACTIVE\_CHILD\_ADDRESS| RAW(8)   | Address of the last used execution plan              |
| LITERAL\_HASH\_VALUE | BIGINT           | Reserved field                                       |
| FIRST\_LOAD\_TIME    | DATE             | Time of the first load of the execution plan         |
| LAST\_LOAD\_TIME     | DATE             | Time of the last load                                 |
| LAST\_ACTIVE\_TIME    | DATE             | Time of the last activation of the execution plan    |
| IS\_OBSOLETE         | VARCHAR(1)       | Indicates whether it is obsolete; might be obsolete if there are too many or too large execution plan versions |
| IS\_BIND\_SENSITIVE   | VARCHAR(1)      | Indicates whether it is sensitive to bind parameters; if the value of bind parameters changes, it needs to be re-optimized |
| BIND\_DATA           | RAW(2000)        | Reserved field                                       |
| LOCKED\_TOTAL        | BIGINT           | Number of times the execution plan has been locked; reserved field |
| PINNED\_TOTAL        | BIGINT           | Number of times the execution plan has been pinned   |
| IS\_REOPTIMIZABLE    | VARCHAR(1)       | Indicates whether it needs to be re-optimized        |
| RESTART\_STATEMENTS   | BIGINT          | Number of statement restarts                          |
| FIRST\_LOAD\_LOCATION | BIGINT          | SQL address information                               |
| BLOCK\_RECEIVED      | BIGINT           | Number of times the latest pages are received from other nodes in the cluster |
| CR\_BLOCK\_RECEIVED  | BIGINT           | Number of times the CR pages are received from other nodes in the cluster |
| LOCAL\_GRANTS        | BIGINT           | Number of times local node has loaded authorized pages in the cluster |
| REMOTE\_GRANTS       | BIGINT           | Number of times other nodes have loaded authorized pages in the cluster |
| LOCAL\_UPGRADES      | BIGINT           | Number of times local node has upgraded page locks in the cluster |
| REMOTE\_UPGRADES     | BIGINT           | Number of times other nodes have upgraded page locks in the cluster |