This view displays all tables, including information about system tables.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| OWNER               | VARCHAR(64)   | The username of the table                                                                  |
| TABLE_NAME          | VARCHAR(64)   | The name of the table                                                                      |
| TABLE_TYPE          | VARCHAR(8)    | The type of the table<br>*  HEAP table<br/>*  TAC table<br/>*  LSC table<br/>*  EXTERNAL table |
| TABLESPACE_NAME     | VARCHAR(64)   | The name of the tablespace where the table is located                                     |
| STATUS              | CHAR(5)      | The status of the table<br>*   VALID: usable                                              |
| PCT_FREE            | INTEGER       | The percentage of space reserved on the block                                             |
| INI_TRANS           | INTEGER       | The initial number of transactions for the block                                          |
| MAX_TRANS           | INTEGER       | The maximum number of transactions for the block                                          |
| LOGGING             | VARCHAR(1)    | Whether to log redo<br>*   Y: logs redo<br>*   N: does not log redo                      |
| CORRUPTED           | CHAR(1)      | Whether the table is corrupted<br>*   Y: table is corrupted<br>*   N: table is not corrupted |
| NUM_ROWS            | BIGINT        | The number of rows in the table                                                            |
| BLOCKS              | BIGINT        | The number of blocks in the table                                                          |
| EMPTY_BLOCKS        | BIGINT        | The number of empty blocks in the table                                                    |
| SAMPLE_SIZE         | BIGINT        | The number of rows sampled for statistics                                                  |
| LAST_ANALYZED      | DATE          | The last time the statistics were collected                                                |
| COMPRESSION         | VARCHAR(12)   | The compression method used for the table; empty indicates no compression, UNCOMPRESSED indicates uncompressed, other values correspond to compression methods |
| COMPRESSION_LEVEL    | VARCHAR(6)    | The compression level of the table; empty indicates no support, with LOW, MEDIUM, and HIGH levels available |
| MCOL_TTL           | BIGINT        | The lifecycle of variable column data for the table, in seconds. Data may be automatically converted to immutable column data after this time |
| PARTITIONED         | VARCHAR(1)    | Whether it is a partitioned table<br>*   Y: partitioned table<br>*   N: not a partitioned table |
| TEMPORARY           | VARCHAR(1)    | Whether it is a temporary table<br>*   Y: temporary table<br>*   N: not a temporary table |
| ROW_MOVEMENT        | VARCHAR(7)    | Whether row movement is allowed<br>*   ENABLE<br>*   DISABLE                             |
| TRANSFORM           | VARCHAR(7)    | Whether LSC tables allow mutable data to be transformed into immutable data<br>*   ENABLE<br>*   DISABLE |
| COMPACT             | VARCHAR(7)    | Whether LSC tables allow merging of immutable data<br>*   ENABLE<br>*   DISABLE           |
| BUILD_AC            | VARCHAR(7)    | Whether LSC tables allow generating AC slice files from immutable data<br>*   ENABLE<br>*   DISABLE |
| MCOL                | VARCHAR(7)    | Whether LSC tables have enabled MCOL capability<br>*   ENABLE<br>*   DISABLE              |
| DATABASE_MAINTAINED  | VARCHAR(1)    | Whether created by the database system <br>* Y: Yes <br>* N: No (created by the user) |
| DURATION            | VARCHAR(15)   | If it is a temporary table, duration indicates sys$session (session-level temporary table) or sys$transaction (transaction-level temporary table), representing the duration of the temporary table; otherwise NULL |
| NESTED              | VARCHAR(1)    | Whether it is a nested table<br>*   Y: nested table<br>*   N: not a nested table           |
| SHARDED             | VARCHAR(1)    | Whether it is a sharded table<br>*   Y: sharded table<br>*   N: not a sharded table      |
| DUPLICATED          | VARCHAR(1)    | Whether it is a duplicated table<br>*   Y: duplicated table<br>*   N: not a duplicated table |
| SEGMENT_DEFERRED    | VARCHAR(1)    | Whether segment creation is deferred<br>*   Y: deferred<br>*   N: not deferred            |
| DEGREE              | INTEGER       | The degree of parallelism for the table                                                   |
| INITIAL_EXTENT      | NUMBER        | The size of the first extent of the segment, in bytes                                     |
| NEXT_EXTENT         | NUMBER        | Compatibility field, has no actual meaning, fixed to be the same as INITIAL_EXTENT       |
| MIN_EXTENTS         | INTEGER       | Compatibility field, has no actual meaning, fixed to 1                                    |
| MAX_EXTENTS         | BIGINT        | Compatibility field, has no actual meaning, fixed to 4294967295                           |