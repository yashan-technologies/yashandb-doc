This view shows a summary of backup or restore progress information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| TYPE | VARCHAR(16) | Type of backup or restore<br>\*   NONE: No backup or restore is currently being performed<br>\*   BACKUP: Backing up<br>\*   RESTORE: Restoring |
| STAGE | VARCHAR(16) | Current stage of backup (restore)<br>\*   none: No backup or restore has been performed<br>\*   start: Started<br>\*   base data file: Baseline backup set's data file stage<br>\*   base bucket file: Baseline backup set's LSC table immutable data file stage<br>\*  ctrl file: Control file stage<br>\*   data file: Data file stage<br>\*   archive file: Archive file stage<br>\*   bucket file: Immutable data file stage of the LSC table<br>\*   profile: Summary file stage<br>\*   extend: File extension stage<br>\*   wait open: Waiting for standby database to open<br>\*   end: Ended |
| STAGE_PROGRESS | NUMBER | Current stage completion percentage, range: \[0, 100\] |
| TOTAL_PROGRESS | NUMBER | Overall progress completion percentage, range: \[0, 100\], estimated value, may differ from actual progress |
| START_TIME | TIMESTAMP(6) | Start time of backup or restore, in the server's local time |
| END_TIME | TIMESTAMP(6) | End time of backup or restore, in the server's local time |
| ELAPSED_TIME | BIGINT | Duration of the backup or restore (unit: seconds) |
| INPUT_BYTES | BIGINT | Total size read (unit: bytes) |
| OUTPUT_BYTES | BIGINT | Total size written (unit: bytes) |
| COMPRESSION_RATIO | NUMBER | Compression ratio percentage. For backup, it is OUTPUT_BYTES / INPUT_BYTES; for restore, it is INPUT_BYTES / OUTPUT_BYTES |
| AVG_INPUT_RATE | NUMBER | Average IO read speed (unit: MB/s). This value represents the average IO read speed of all backup and restore sub-threads, which differs from the value obtained by INPUT_BYTES / ELAPSED_TIME |
| CUR_INPUT_RATE | NUMBER | Most recent IO read speed (unit: MB/s) |
| MAX_INPUT_RATE | NUMBER | Maximum IO read speed (unit: MB/s) |
| MIN_INPUT_RATE | NUMBER | Minimum IO read speed (unit: MB/s) |
| AVG_OUTPUT_RATE | NUMBER | Average IO write speed (unit: MB/s). This value represents the average IO write speed of all backup and restore sub-threads, which differs from the value obtained by OUTPUT_BYTES / ELAPSED_TIME |
| CUR_OUTPUT_RATE | NUMBER | Most recent IO write speed (unit: MB/s) |
| MAX_OUTPUT_RATE | NUMBER | Maximum IO write speed (unit: MB/s) |
| MIN_OUTPUT_RATE | NUMBER | Minimum IO write speed (unit: MB/s) |