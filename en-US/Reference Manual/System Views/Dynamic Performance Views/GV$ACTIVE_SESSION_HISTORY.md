ASH view, displaying historical session information collected by the MMNL background thread.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | NUMBER       | Group ID                                             |
| GROUP\_NODE\_ID | NUMBER       | Node ID within the group                             |
| INST\_ID | NUMBER       | Instance ID                                          |
|  SAMPLE\_ID | BIGINT       | Sample ID                                             |
| SAMPLE\_TIME | TIMESTAMP(6) | Sample time                                           |
| SAMPLE\_TIME\_UTC | TIMESTAMP(6) | Sample UTC time                                       |
| USECS\_PER\_ROW | BIGINT       | Time consumed for collection                          |
| IS\_AWR\_SAMPLE | VARCHAR(1)   | Indicates whether this sample has been flushed or will be flushed to the Automatic Workload Repository |
| SESSION\_ID | INTEGER      | Session ID                                            |
| SESSION\_SERIAL# | INTEGER      | Session serial number                                 |
| SESSION\_TYPE | VARCHAR(10)  | Session type                                          |
| FLAGS | BIGINT       | (Reserved field)                                      |
| USER\_ID | INTEGER      | User ID                                               |
| SQL\_ID | VARCHAR(13)  | SQL identifier of the SQL statement being executed by the session at sampling time |
| IS\_SQLID\_CURRENT | VARCHAR(1)   | (Reserved field)                                      |
| SQL\_CHILD\_NUMBER | INTEGER      | (Reserved field)                                      |
| SQL\_OPCODE | INTEGER      | SQL command type value                               |
| SQL\_OPNAME | VARCHAR(64)  | SQL command name                                      |
| FORCE\_MATCHING\_SIGNATURE | NUMBER       | (Reserved field)                                      |
| TOP\_LEVEL\_SQL\_ID | VARCHAR(13)  | SQL identifier of the top-level SQL statement         |
| TOP\_LEVEL\_SQL\_OPCODE | INTEGER      | Top-level SQL statement command type value            |
| SQL\_ADAPTIVE\_PLAN\_RESOLVED | BIGINT       | (Reserved field)                                      |
| SQL\_FULL\_PLAN\_HASH\_VALUE | BIGINT       | (Reserved field)                                      |
| SQL\_PLAN\_HASH\_VALUE | BIGINT       | Numeric representation of the cursor's SQL execution plan |
| SQL\_PLAN\_LINE\_ID | BIGINT       | (Reserved field)                                      |
| SQL\_PLAN\_OPERATION | VARCHAR(64)  | Plan operation name                                   |
| SQL\_PLAN\_OPTIONS | VARCHAR(64)  | (Reserved field)                                      |
| SQL\_EXEC\_ID | BIGINT       | (Reserved field)                                      |
| SQL\_EXEC\_START | DATE         | SQL execution start time                              |
| PLSQL\_ENTRY\_OBJECT\_ID | BIGINT       | Object ID of the PL subprogram at the top of the stack; NULL if there is no PL subprogram on the stack |
| PLSQL\_ENTRY\_SUBPROGRAM\_ID | BIGINT       | Subprogram ID of the topmost PL subprogram in the stack |
| PLSQL\_OBJECT\_ID | BIGINT       | Object ID of the PL subprogram currently being executed |
| PLSQL\_SUBPROGRAM\_ID | BIGINT       | Subprogram ID of the PL object currently being executed; NULL if SQL is being executed |
| QC\_INSTANCE\_ID | INTEGER      | (Reserved field)                                      |
| QC\_SESSION\_ID | INTEGER      | (Reserved field)                                      |
| QC\_SESSION\_SERIAL# | INTEGER      | (Reserved field)                                      |
| PX\_FLAGS | BIGINT       | (Reserved field)                                      |
| EVENT | VARCHAR(64)  | Wait event name                                       |
| EVENT\_ID | INTEGER      | Wait event ID                                         |
| EVENT# | INTEGER      | Wait event number                                     |
| SEQ# | BIGINT       | (Reserved field)                                      |
| P1TEXT | VARCHAR(64)  | (Reserved field)                                      |
| P1 | NUMBER       | (Reserved field)                                      |
| P2TEXT | VARCHAR(64)  | (Reserved field)                                      |
| P2 | NUMBER       | (Reserved field)                                      |
| P3TEXT | VARCHAR(64)  | (Reserved field)                                      |
| P3 | NUMBER       | (Reserved field)                                      |
| WAIT\_CLASS | VARCHAR(64)  | Wait event class name                                  |
| WAIT\_CLASS\_ID | INTEGER      | Wait event class ID                                    |
| WAIT\_TIME | BIGINT       | If the session was running on CPU at sampling time, indicates the total wait time of the session's previous wait event; 0 indicates the session was waiting at sampling time |
| SESSION\_STATE | VARCHAR(7)   | Session state, ON CPU: CPU in use<br>\*   WAITING: Waiting for non-idle events |
| TIME\_WAITED | BIGINT       | If SESSION_STATE is WAITING, indicates the actual time the session waited for this event (in microseconds). This column is only set for wait events that were in progress at sampling time. If a wait event lasts longer than one second and is recorded across multiple session sample rows, the actual wait time for that event is recorded in the last session sample row |
| BLOCKING\_SESSION\_STATUS | VARCHAR(11)  | (Reserved field)                                      |
| BLOCKING\_SESSION | INTEGER      | (Reserved field)                                      |
| BLOCKING\_SESSION\_SERIAL# | INTEGER      | (Reserved field)                                      |
| BLOCKING\_INST\_ID | INTEGER      | (Reserved field)                                      |
| BLOCKING\_HANGCHAIN\_INFO | VARCHAR(1)   | (Reserved field)                                      |
| CURRENT\_OBJ# | BIGINT       | (Reserved field)                                      |
| CURRENT\_FILE# | BIGINT       | (Reserved field)                                      |
| CURRENT\_BLOCK# | BIGINT       | (Reserved field)                                      |
| CURRENT\_ROW# | BIGINT       | (Reserved field)                                      |
| TOP\_LEVEL\_CALL# | BIGINT       | (Reserved field)                                      |
| TOP\_LEVEL\_CALL\_NAME | VARCHAR(64)  | (Reserved field)                                      |
| CONSUMER\_GROUP\_ID | INTEGER      | (Reserved field)                                      |
| XID | BIGINT       | Global transaction ID                                 |
| REMOTE\_INSTANCE# | INTEGER      | (Reserved field)                                      |
| TIME\_MODEL | BIGINT       | Time model information                                 |
| IN\_CONNECTION\_MGMT | VARCHAR(1)   | Indicates whether the session was in connection management at sampling time (Y) or not (N) |
| IN\_PARSE | VARCHAR(1)   | Indicates whether the session was parsing at sampling time (Y) or not (N) |
| IN\_HARD\_PARSE | VARCHAR(1)   | Indicates whether the session was in hard parse state at sampling time (Y) or not (N) |
| IN\_SQL\_EXECUTION | VARCHAR(1)   | Indicates whether the session was executing SQL at sampling time (Y) or not (N) |
| IN\_PLSQL\_EXECUTION | VARCHAR(1)   | Indicates whether the session was executing PL at sampling time (Y) or not (N) |
| IN\_PLSQL\_RPC | VARCHAR(1)   | (Reserved field)                                      |
| IN\_PLSQL\_COMPILATION | VARCHAR(1)   | Indicates whether the session was compiling PL at sampling time (Y) or not (N) |
| IN\_JAVA\_EXECUTION | VARCHAR(1)   | (Reserved field)                                      |
| IN\_BIND | VARCHAR(1)   | Indicates whether the session was performing bind operations at sampling time (Y) or not (N) |
| IN\_CURSOR\_CLOSE | VARCHAR(1)   | Indicates whether the session was closing a cursor at sampling time (Y) or not (N) |
| IN\_SEQUENCE\_LOAD | VARCHAR(1)   | (Reserved field)                                      |
| IN\_INMEMORY\_QUERY | VARCHAR(1)   | (Reserved field)                                      |
| IN\_INMEMORY\_POPULATE | VARCHAR(1)   | (Reserved field)                                      |
| IN\_INMEMORY\_PREPOPULATE | VARCHAR(1)   | (Reserved field)                                      |
| IN\_INMEMORY\_REPOPULATE | VARCHAR(1)   | (Reserved field)                                      |
| IN\_INMEMORY\_TREPOPULATE | VARCHAR(1)   | (Reserved field)                                      |
| IN\_TABLESPACE\_ENCRYPTION | VARCHAR(1)   | Indicates whether tablespace encryption or decryption occurred at sampling time (Y) or not (N) |
| CAPTURE\_OVERHEAD | VARCHAR(1)   | (Reserved field)                                      |
| REPLAY\_OVERHEAD | VARCHAR(1)   | (Reserved field)                                      |
| IS\_CAPTURED | VARCHAR(1)   | (Reserved field)                                      |
| IS\_REPLAYED | VARCHAR(1)   | (Reserved field)                                      |
| IS\_REPLAY\_SYNC\_TOKEN\_HOLDER | VARCHAR(1)   | (Reserved field)                                      |
| SERVICE\_HASH | BIGINT       | (Reserved field)                                      |
| PROGRAM | VARCHAR(64)  | Client program name (yasql, JDBC Driver, C Driver, Go Driver...) |
| MODULE | VARCHAR(64)  | Name of the currently executing module, set by calling DBMS_APPLICATION_INFO.SET_MODULE procedure |
| ACTION | VARCHAR(64)  | Name of the action currently being executed, set by calling related procedures in the DBMS_APPLICATION_INFO package |
| CLIENT\_ID | VARCHAR(64)  | Client identifier for the current session               |
| MACHINE | VARCHAR(64)  | Client operating system name                            |
| PORT | INTEGER      | Client port number                                     |
| ECID | VARCHAR(64)  | (Reserved field)                                      |
| DBREPLAY\_FILE\_ID | BIGINT       | (Reserved field)                                      |
| DBREPLAY\_CALL\_COUNTER | BIGINT       | (Reserved field)                                      |
| TM\_DELTA\_TIME | BIGINT       | Time interval over which TM_DELTA_CPU_TIME and TM_DELTA_DB_TIME are accumulated (in microseconds) |
| TM\_DELTA\_CPU\_TIME | BIGINT       | Time this session spent on CPU in the past TM_DELTA_TIME microseconds (in microseconds) |
| TM\_DELTA\_DB\_TIME | BIGINT       | Time this session spent in database calls in the past TM_DELTA_TIME microseconds (in microseconds) |
| DELTA\_TIME | BIGINT       | (Reserved field)                                      |
| DELTA\_READ\_IO\_REQUESTS | BIGINT       | (Reserved field)                                      |
| DELTA\_WRITE\_IO\_REQUESTS | BIGINT       | (Reserved field)                                      |
| DELTA\_READ\_IO\_BYTES | BIGINT       | (Reserved field)                                      |
| DELTA\_WRITE\_IO\_BYTES | BIGINT       | (Reserved field)                                      |
| DELTA\_INTERCONNECT\_IO\_BYTES | BIGINT       | (Reserved field)                                      |
| DELTA\_READ\_MEM\_BYTES | BIGINT       | (Reserved field)                                      |
| PGA\_ALLOCATED | BIGINT       | (Reserved field)                                      |
| TEMP\_SPACE\_ALLOCATED | BIGINT       | (Reserved field)                                      |
| CON\_DBID | BIGINT       | (Reserved field)                                      |
| CON\_ID | INTEGER      | (Reserved field)                                      |
| DBOP\_NAME | VARCHAR(30)  | (Reserved field)                                      |
| DBOP\_EXEC\_ID | INTEGER      | (Reserved field)                                      |
