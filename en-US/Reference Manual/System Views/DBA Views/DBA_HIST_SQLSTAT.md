This view displays SQL statement statistics captured in the AWR repository. This view is used together with the DBA_HIST_SQLTEXT view to view historical SQL statistics and text content.

|  Field  | Type  | Description  |
| --- | --- | --- |
| SNAP_ID | NUMBER | AWR snapshot ID |
| DBID | NUMBER | Database ID |
| INSTANCE_NUMBER | NUMBER | Instance number |
| SQL_ID | VARCHAR(13) | SQL identifier |
| PLAN_HASH_VALUE | NUMBER | Execution plan hash value |
| OPTIMIZER_COST | NUMBER | Optimizer cost |
| OPTIMIZER_MODE | VARCHAR(10) | Optimizer mode |
| OPTIMIZER_ENV_HASH_VALUE | NUMBER | Optimizer environment hash value |
| SHARABLE_MEM | NUMBER | Shareable memory |
| LOADED_VERSIONS | NUMBER | Loaded versions |
| VERSION_COUNT | NUMBER | Version count |
| MODULE | VARCHAR(64) | Module name |
| ACTION | VARCHAR(64) | Action name |
| SQL_PROFILE | VARCHAR(64) | SQL profile |
| FORCE_MATCHING_SIGNATURE | NUMBER | Force matching signature |
| PARSING_SCHEMA_ID | NUMBER | Parsing schema ID |
| PARSING_SCHEMA_NAME | VARCHAR(128) | Parsing schema name |
| PARSING_USER_ID | NUMBER | Parsing user ID |
| FETCHES_TOTAL | NUMBER | Total fetch count |
| FETCHES_DELTA | NUMBER | Fetch delta count |
| END_OF_FETCH_COUNT_TOTAL | NUMBER | Total end-of-fetch count |
| END_OF_FETCH_COUNT_DELTA | NUMBER | End-of-fetch delta count |
| SORTS_TOTAL | NUMBER | Total sort count |
| SORTS_DELTA | NUMBER | Sort delta count |
| EXECUTIONS_TOTAL | NUMBER | Total execution count |
| EXECUTIONS_DELTA | NUMBER | Execution delta count |
| PX_SERVERS_EXECS_TOTAL | NUMBER | Total parallel execution server count |
| PX_SERVERS_EXECS_DELTA | NUMBER | Parallel execution server delta count |
| LOADS_TOTAL | NUMBER | Total load count |
| LOADS_DELTA | NUMBER | Load delta count |
| INVALIDATIONS_TOTAL | NUMBER | Total invalidation count |
| INVALIDATIONS_DELTA | NUMBER | Invalidation delta count |
| PARSE_CALLS_TOTAL | NUMBER | Total parse call count |
| PARSE_CALLS_DELTA | NUMBER | Parse call delta count |
| DISK_READS_TOTAL | NUMBER | Total disk read count |
| DISK_READS_DELTA | NUMBER | Disk read delta count |
| BUFFER_GETS_TOTAL | NUMBER | Total buffer get count |
| BUFFER_GETS_DELTA | NUMBER | Buffer get delta count |
| ROWS_PROCESSED_TOTAL | NUMBER | Total rows processed |
| ROWS_PROCESSED_DELTA | NUMBER | Rows processed delta |
| CPU_TIME_TOTAL | NUMBER | Total CPU time (unit: microseconds) |
| CPU_TIME_DELTA | NUMBER | CPU time delta (unit: microseconds) |
| ELAPSED_TIME_TOTAL | NUMBER | Total elapsed time (unit: microseconds) |
| ELAPSED_TIME_DELTA | NUMBER | Elapsed time delta (unit: microseconds) |
| IOWAIT_TOTAL | NUMBER | Total I/O wait time |
| IOWAIT_DELTA | NUMBER | I/O wait time delta |
| CLWAIT_TOTAL | NUMBER | Total cluster wait time |
| CLWAIT_DELTA | NUMBER | Cluster wait time delta |
| APWAIT_TOTAL | NUMBER | Total application wait time |
| APWAIT_DELTA | NUMBER | Application wait time delta |
| CCWAIT_TOTAL | NUMBER | Total parallel query coordinator wait time |
| CCWAIT_DELTA | NUMBER | Parallel query coordinator wait time delta |
| DIRECT_WRITES_TOTAL | NUMBER | Total direct write count |
| DIRECT_WRITES_DELTA | NUMBER | Direct write delta count |
| PLSEXEC_TIME_TOTAL | NUMBER | Total PL execution time |
| PLSEXEC_TIME_DELTA | NUMBER | PL execution time delta |
| JAVEXEC_TIME_TOTAL | NUMBER | Total Java execution time |
| JAVEXEC_TIME_DELTA | NUMBER | Java execution time delta |
| IO_INTERCONNECT_BYTES_TOTAL | NUMBER | Total interconnect bytes |
| IO_INTERCONNECT_BYTES_DELTA | NUMBER | Interconnect bytes delta |
| PHYSICAL_READ_REQUESTS_TOTAL | NUMBER | Total physical read requests |
| PHYSICAL_READ_REQUESTS_DELTA | NUMBER | Physical read requests delta |
| PHYSICAL_READ_BYTES_TOTAL | NUMBER | Total physical read bytes |
| PHYSICAL_READ_BYTES_DELTA | NUMBER | Physical read bytes delta |
| PHYSICAL_WRITE_REQUESTS_TOTAL | NUMBER | Total physical write requests |
| PHYSICAL_WRITE_REQUESTS_DELTA | NUMBER | Physical write requests delta |
| PHYSICAL_WRITE_BYTES_TOTAL | NUMBER | Total physical write bytes |
| PHYSICAL_WRITE_BYTES_DELTA | NUMBER | Physical write bytes delta |
| BIND_DATA | RAW(2000) | Bind data |
| FLAG | NUMBER | Flag |
| OBSOLETE_COUNT | NUMBER | Obsolete count |