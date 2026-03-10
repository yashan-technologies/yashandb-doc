This view displays the statistics of the YStream server operation.

|Field |Type |Description |
|-------|------|------|
| SERVER_ID            | INTEGER     | Service ID                               |
| SERVER_NAME          | VARCHAR(64) | Service name                             |
| START_TIME           | TIMESTAMP   | Start time                               |
| STOP_TIME            | TIMESTAMP   | Stop time                                |
| CAPTURE_INSTANCE_ID  | INTEGER     | Node ID of the last parsed log          |
| CAPTURE_LFN         | BIGINT      | Parsed redo log LFN                      |
| CAPTURE_SCN         | BIGINT      | Parsed redo log SCN                      |
| CAPTURE_SCN_LOCAL_TIME | TIMESTAMP | Local time corresponding to the parsed redo log SCN |
| SEND_SIZE            | BIGINT      | Size of sent LCR (in bytes)            |
| SEND_COUNT           | BIGINT      | Number of sent LCR                      |
| CAPTURE_SIZE         | BIGINT      | Size of parsed log (in bytes)          |
| CAPTURE_COUNT        | BIGINT      | Number of parsed LCR                    |
| SPILL_SIZE           | BIGINT      | Size of spilled transaction LCR (in bytes) |
| SPILL_COUNT          | BIGINT      | Number of spilled transaction LCR       |
| REDO_READ_COUNT      | BIGINT      | Redo read count                         |
| REDO_SORT_COUNT      | BIGINT      | Redo sort count                         |
| RECORD_DECODE_COUNT  | BIGINT      | LCR decode count                        |
| RECORD_FETCH_COUNT    | BIGINT      | LCR fetch count                         |
| REDO_READ_TIME       | BIGINT      | Redo read time (in microseconds)       |
| REDO_SORT_TIME       | BIGINT      | Redo sort time (in microseconds)       |
| RECORD_DECODE_TIME   | BIGINT      | LCR decode time (in microseconds)      |
| RECORD_FETCH_TIME    | BIGINT      | LCR fetch time (in microseconds)       |
| XACT_MEMORY_USED     | BIGINT      | Memory used by active transactions (in bytes) |
| SPILL_MEMORY_USED    | BIGINT      | Memory used by spilled transactions (in bytes) |
| DDL_MEMORY_USED      | BIGINT      | Memory used by DDL (in bytes)          |