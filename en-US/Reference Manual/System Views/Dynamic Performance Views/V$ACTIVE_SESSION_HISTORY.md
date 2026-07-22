ASH view, displaying historical session activity information collected by the MMNL background thread.

|  Field  | Type           | Description  |
| --- |--------------| --- |
| SAMPLE_ID | BIGINT      | Sample ID                                                |
| SAMPLE_TIME | TIMESTAMP(6)   | Sample time                                                |
| SAMPLE_TIME_UTC | TIMESTAMP(6)   | Sample UTC time                                             |
| USECS_PER_ROW | BIGINT      | Time consumed for collection                                             |
| IS_AWR_SAMPLE | VARCHAR(1)     | Indicates whether this sample has been or will be flushed to the Automatic Workload Repository                          |
| SESSION_ID | INTEGER     | Session ID                                                |
| SESSION_SERIAL# | INTEGER     | Session serial number                                                |
| SESSION_TYPE | VARCHAR(10) | Session type                                                |
| FLAGS | BIGINT      | (Reserved field)                                              |
| USER_ID | INTEGER     | User ID                                                |
| SQL_ID | VARCHAR(13) | SQL identifier of the SQL statement being executed by the session at sampling time                         |
| IS_SQLID_CURRENT | VARCHAR(1)  | (Reserved field)                                              |
| SQL_CHILD_NUMBER | INTEGER     | (Reserved field)                                              |
| SQL_OPCODE | INTEGER     | SQL command type value                                            |
| SQL_OPNAME | VARCHAR(64) | SQL command name                                              |
| FORCE_MATCHING_SIGNATURE | NUMBER      | (Reserved field)                                              |
| TOP_LEVEL_SQL_ID | VARCHAR(13) | SQL identifier of the top-level SQL statement                                      |
| TOP_LEVEL_SQL_OPCODE | INTEGER     | Top-level SQL statement command type value                                        |
| SQL_ADAPTIVE_PLAN_RESOLVED | BIGINT      | (Reserved field)                                              |
| SQL_FULL_PLAN_HASH_VALUE | BIGINT      | (Reserved field)                                              |
| SQL_PLAN_HASH_VALUE | BIGINT      | Numeric representation of the cursor's SQL execution plan                                   |
| SQL_PLAN_LINE_ID | BIGINT      | (Reserved field)                                              |
| SQL_PLAN_OPERATION | VARCHAR(64) | Plan operation name                                              |
| SQL_PLAN_OPTIONS | VARCHAR(64) | (Reserved field)                                              |
| SQL_EXEC_ID | BIGINT      | (Reserved field)                                              |
| SQL_EXEC_START | DATE        | SQL execution start time                                           |
| PLSQL_ENTRY_OBJECT_ID | BIGINT      | Object ID of the PL subprogram at the top of the stack; NULL if there is no PL subprogram on the stack |
| PLSQL_ENTRY_SUBPROGRAM_ID | BIGINT      | Subprogram ID of the topmost PL subprogram in the stack                         |
| PLSQL_OBJECT_ID | BIGINT      | Object ID of the PL subprogram currently being executed                         |
| PLSQL_SUBPROGRAM_ID | BIGINT      | Subprogram ID of the PL object currently being executed; NULL if SQL is being executed      |
| QC_INSTANCE_ID | INTEGER     | (Reserved field)                                              |
| QC_SESSION_ID | INTEGER     | (Reserved field)                                              |
| QC_SESSION_SERIAL# | INTEGER     | (Reserved field)                                              |
| PX_FLAGS | BIGINT      | (Reserved field)                                              |
| EVENT | VARCHAR(64) | Wait event name                                              |
| EVENT_ID | INTEGER     | Wait event ID                                              |
| EVENT# | INTEGER     | Wait event ID                                              |
| SEQ# | BIGINT      | (Reserved field)                                              |
| P1TEXT | VARCHAR(64) | (Reserved field)                                              |
| P1 | NUMBER      | (Reserved field)                                              |
| P2TEXT | VARCHAR(64) | (Reserved field)                                              |
| P2 | NUMBER      | (Reserved field)                                              |
| P3TEXT | VARCHAR(64) | (Reserved field)                                              |
| P3 | NUMBER      | (Reserved field)                                              |
| WAIT_CLASS | VARCHAR(64) | Wait event class name                                            |
| WAIT_CLASS_ID | INTEGER     | Wait event class ID                                            |
| WAIT_TIME | BIGINT      | If the session was running on CPU at sampling time, this represents the total wait time of the session's previous wait event; 0 indicates the session was waiting at sampling time |
| SESSION_STATE | VARCHAR(7)  | Session state, ON CPU or WAITING                                 |
| TIME_WAITED | BIGINT      | If SESSION_STATE is WAITING, this represents the actual time the session spent waiting for this event (unit: microseconds) <br/>This column is set only for wait events that were in progress at sampling time. If a wait event lasts longer than one second and is recorded across multiple session sample rows, the actual wait time for that event is recorded in the last session sample row. |
| BLOCKING_SESSION_STATUS | VARCHAR(11) | (Reserved field)                                              |
| BLOCKING_SESSION | INTEGER     | (Reserved field)                                              |
| BLOCKING_SESSION_SERIAL# | INTEGER     | (Reserved field)                                              |
| BLOCKING_INST_ID | INTEGER     | (Reserved field)                                              |
| BLOCKING_HANGCHAIN_INFO | VARCHAR(1)  | (Reserved field)                                              |
| CURRENT_OBJ# | BIGINT      | (Reserved field)                                              |
| CURRENT_FILE# | BIGINT      | (Reserved field)                                              |
| CURRENT_BLOCK# | BIGINT      | (Reserved field)                                              |
| CURRENT_ROW# | BIGINT      | (Reserved field)                                              |
| TOP_LEVEL_CALL# | BIGINT      | (Reserved field)                                              |
| TOP_LEVEL_CALL_NAME | VARCHAR(64) | (Reserved field)                                              |
| CONSUMER_GROUP_ID | INTEGER     | (Reserved field)                                              |
| XID | BIGINT      | Global transaction ID                                             |
| REMOTE_INSTANCE# | INTEGER     | (Reserved field)                                              |
| TIME_MODEL | BIGINT      | Time model information                                              |
| IN_CONNECTION_MGMT | VARCHAR(1)  | Indicates whether the session was in connection management (Y/N)                                         |
| IN_PARSE | VARCHAR(1)  | Indicates whether the session was parsing (Y/N)                                             |
| IN_HARD_PARSE | VARCHAR(1)  | Indicates whether the session was in hard parse (Y/N)                                           |
| IN_SQL_EXECUTION | VARCHAR(1)  | Indicates whether the session was executing SQL (Y/N)                                           |
| IN_PLSQL_EXECUTION | VARCHAR(1)  | Indicates whether the session was executing PL (Y/N)                                           |
| IN_PLSQL_RPC | VARCHAR(1)  | (Reserved field)                                              |
| IN_PLSQL_COMPILATION | VARCHAR(1)  | Indicates whether the session was compiling PL (Y/N)                                           |
| IN_JAVA_EXECUTION | VARCHAR(1)  | (Reserved field)                                              |
| IN_BIND | VARCHAR(1)  | Indicates whether the session was performing bind operations (Y/N)                                           |
| IN_CURSOR_CLOSE | VARCHAR(1)  | Indicates whether the session was closing the cursor (Y/N)                                            |
| IN_SEQUENCE_LOAD | VARCHAR(1)  | (Reserved field)                                              |
| IN_INMEMORY_QUERY | VARCHAR(1)  | (Reserved field)                                              |
| IN_INMEMORY_POPULATE | VARCHAR(1)  | (Reserved field)                                              |
| IN_INMEMORY_PREPOPULATE | VARCHAR(1)  | (Reserved field)                                              |
| IN_INMEMORY_REPOPULATE | VARCHAR(1)  | (Reserved field)                                              |
| IN_INMEMORY_TREPOPULATE | VARCHAR(1)  | (Reserved field)                                              |
| IN_TABLESPACE_ENCRYPTION | VARCHAR(1)  | Indicates whether tablespace encryption or decryption occurred (Y/N)                                            |
| CAPTURE_OVERHEAD | VARCHAR(1)  | (Reserved field)                                              |
| REPLAY_OVERHEAD | VARCHAR(1)  | (Reserved field)                                              |
| IS_CAPTURED | VARCHAR(1)  | (Reserved field)                                              |
| IS_REPLAYED | VARCHAR(1)  | (Reserved field)                                              |
| IS_REPLAY_SYNC_TOKEN_HOLDER | VARCHAR(1)  | (Reserved field)                                              |
| SERVICE_HASH | BIGINT      | (Reserved field)                                              |
| PROGRAM | VARCHAR(64) | Client program name (yasql, JDBC Driver, C Driver, Go Driver...)     |
| MODULE | VARCHAR(64) | Name of the currently executing module, set by calling the DBMS_APPLICATION_INFO.SET_MODULE procedure |
| ACTION | VARCHAR(64) | Name of the currently executing action, set by calling related procedures in the DBMS_APPLICATION_INFO package    |
| CLIENT_ID | VARCHAR(64) | Client identifier of the current session                                         |
| MACHINE | VARCHAR(64) | Client operating system name                                            |
| PORT | INTEGER     | Client port number                                              |
| ECID | VARCHAR(64) | (Reserved field)                                              |
| DBREPLAY_FILE_ID | BIGINT      | (Reserved field)                                              |
| DBREPLAY_CALL_COUNTER | BIGINT      | (Reserved field)                                              |
| TM_DELTA_TIME | BIGINT      | Time interval (unit: microseconds) over which TM_DELTA_CPU_TIME and TM_DELTA_DB_TIME are accumulated |
| TM_DELTA_CPU_TIME | BIGINT      | Time (unit: microseconds) this session spent on CPU in the past TM_DELTA_TIME microseconds        |
| TM_DELTA_DB_TIME | BIGINT      | Time (unit: microseconds) this session spent in database calls in the past TM_DELTA_TIME microseconds        |
| DELTA_TIME | BIGINT      | (Reserved field)                                              |
| DELTA_READ_IO_REQUESTS | BIGINT      | (Reserved field)                                              |
| DELTA_WRITE_IO_REQUESTS | BIGINT      | (Reserved field)                                              |
| DELTA_READ_IO_BYTES | BIGINT      | (Reserved field)                                              |
| DELTA_WRITE_IO_BYTES | BIGINT      | (Reserved field)                                              |
| DELTA_INTERCONNECT_IO_BYTES | BIGINT      | (Reserved field)                                              |
| DELTA_READ_MEM_BYTES | BIGINT      | (Reserved field)                                              |
| PGA_ALLOCATED | BIGINT      | (Reserved field)                                              |
| TEMP_SPACE_ALLOCATED | BIGINT      | (Reserved field)                                              |
| CON_DBID | BIGINT      | (Reserved field)                                              |
| CON_ID | INTEGER     | (Reserved field)                                              |
| DBOP_NAME | VARCHAR(30) | (Reserved field)                                              |
| DBOP_EXEC_ID | INTEGER     | (Reserved field)                                              |