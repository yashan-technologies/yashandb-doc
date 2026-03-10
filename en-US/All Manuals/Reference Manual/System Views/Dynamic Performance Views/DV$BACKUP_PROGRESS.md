This view displays a summary of the backup or recovery progress information for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER   | Group ID                                                                                                  |
| GROUP_NODE_ID        | INTEGER   | Node ID within the group                                                                                  |
| TYPE                 | VARCHAR(16)| Type of backup or recovery<br>\*   NONE: No backup or recovery currently executed<br>\*   BACKUP: Currently backing up<br>\*   RESTORE: Currently restoring |
| STAGE                | VARCHAR(16)| Current stage of the backup (restore)<br>\*   none: No backup or recovery executed<br>\*   start: Started<br>\*   base data file: Baseline backup set data file stage<br>\*   base bucket file: Baseline backup set LSC table immutable data file stage<br>\*   ctrl file: Control file stage<br>\*   data file: Data file stage<br>\*   archive file: Archive file stage<br>\*   bucket file: Immutable data file stage of the LSC table<br>\*   profile: Summary file stage<br>\*   extend: File extension stage<br>\*   wait open: Waiting for standby database to open<br>\*   end: Ended   |
| STAGE_PROGRESS        | NUMBER    | Current stage completion percentage, range: [0, 100]                                                    |
| TOTAL_PROGRESS        | NUMBER    | Overall progress completion percentage, range: [0, 100], an estimated value that may differ from the actual progress |
| START_TIME            | TIMESTAMP | Backup recovery start time, in server local time                                                          |
| END_TIME              | TIMESTAMP | Backup recovery end time, in server local time                                                            |
| ELAPSED_TIME          | BIGINT    | Duration of backup recovery (in seconds)                                                                  |
| INPUT_BYTES           | BIGINT    | Total size read (in bytes)                                                                                 |
| OUTPUT_BYTES          | BIGINT    | Total size written (in bytes)                                                                               |
| COMPRESSION_RATIO     | NUMBER    | Compression ratio percentage. For backup, it's OUTPUT_BYTES / INPUT_BYTES; for recovery, it's INPUT_BYTES / OUTPUT_BYTES |
| AVG_INPUT_RATE        | NUMBER    | Average IO read speed (in MB/s). This value represents the average IO read speed of all backup recovery sub-threads, which differs from the value obtained by INPUT_BYTES / ELAPSED_TIME |
| CUR_INPUT_RATE        | NUMBER    | Latest IO read speed (in MB/s)                                                                              |
| MAX_INPUT_RATE        | NUMBER    | Maximum IO read speed (in MB/s)                                                                             |
| MIN_INPUT_RATE        | NUMBER    | Minimum IO read speed (in MB/s)                                                                             |
| AVG_OUTPUT_RATE       | NUMBER    | Average IO write speed (in MB/s). This value represents the average IO write speed of all backup recovery sub-threads, which differs from the value obtained by OUTPUT_BYTES / ELAPSED_TIME |
| CUR_OUTPUT_RATE       | NUMBER    | Latest IO write speed (in MB/s)                                                                             |
| MAX_OUTPUT_RATE       | NUMBER    | Maximum IO write speed (in MB/s)                                                                            |
| MIN_OUTPUT_RATE       | NUMBER    | Minimum IO write speed (in MB/s)                                                                            |