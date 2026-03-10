This view shows a summary of the progress information for backup or restore operations.

|Field |Type |Description |
| --- | --- | --- |
| TYPE               | VARCHAR(16)  | Type of backup/restore<br>\*   NONE: No backup or restore operation currently executed<br>\*   BACKUP: Currently backing up<br>\*   RESTORE: Currently restoring |
| STAGE              | VARCHAR(16)  | Current stage of backup (restore)<br>\*   none: No backup or restore operation executed<br>\*   start: Started<br>\*   base data file: Base backup set's data file stage<br>\*   base bucket file: Base backup set's LSC non-modifiable data file stage<br>\*   ctrl file: Control file stage<br>\*   archive prepare: Archive backup set restore scan preparation stage<br>\*   data file: Data file stage<br>\*   archive file: Archive file stage<br>\*   bucket file: LSC table's non-modifiable data file stage<br>\*   profile: Summary file stage<br>\*   extend: File extension stage<br>\*   wait open: Waiting for standby database to open<br>\*   end: Finished |
| STAGE_PROGRESS     | NUMBER       | Percentage of completion for the current stage, range: \[0, 100\] |
| TOTAL_PROGRESS     | NUMBER       | Overall completion percentage, range: \[0, 100\], an estimated value that may differ from actual progress |
| START_TIME         | TIMESTAMP(6)    | Backup/restore start time, in server local time             |
| END_TIME           | TIMESTAMP(6)    | Backup/restore end time, in server local time               |
| ELAPSED_TIME       | BIGINT       | Duration of backup/restore (in seconds)                     |
| INPUT_BYTES        | BIGINT       | Total size read (in bytes)                                  |
| OUTPUT_BYTES       | BIGINT       | Total size written (in bytes)                               |
| COMPRESSION_RATIO   | NUMBER       | Compression ratio percentage. For backup: OUTPUT_BYTES / INPUT_BYTES, for restore: INPUT_BYTES / OUTPUT_BYTES |
| AVG_INPUT_RATE     | NUMBER       | Average IO read speed (in MB/s). This value represents the average IO read speed of all backup/restore sub-threads, which may differ from the value obtained by INPUT_BYTES / ELAPSED_TIME |
| CUR_INPUT_RATE     | NUMBER       | Most recent IO read speed (in MB/s)                         |
| MAX_INPUT_RATE     | NUMBER       | Maximum IO read speed (in MB/s)                             |
| MIN_INPUT_RATE     | NUMBER       | Minimum IO read speed (in MB/s)                             |
| AVG_OUTPUT_RATE    | NUMBER       | Average IO write speed (in MB/s). This value represents the average IO write speed of all backup/restore sub-threads, which may differ from the value obtained by OUTPUT_BYTES / ELAPSED_TIME |
| CUR_OUTPUT_RATE    | NUMBER       | Most recent IO write speed (in MB/s)                        |
| MAX_OUTPUT_RATE    | NUMBER       | Maximum IO write speed (in MB/s)                            |
| MIN_OUTPUT_RATE    | NUMBER       | Minimum IO write speed (in MB/s)                            |