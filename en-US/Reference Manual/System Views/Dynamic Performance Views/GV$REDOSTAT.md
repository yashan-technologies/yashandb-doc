This view shows the statistics of redo performance.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | NUMBER  | Group ID                                                                    |
| GROUP_NODE_ID        | NUMBER  | Node ID within the group                                                    |
| INST_ID              | NUMBER  | Instance ID                                                                 |
| REDO_FLUSH_SPEED     | BIGINT  | Current redo flush speed (average speed over the last 3 seconds) (unit: KB/s) |
| CHECKPOINT_SPEED      | BIGINT  | Current speed of checkpoint progressing the redo apply point (average speed over the last 3 seconds) (unit: KB/s) |
| AVR_CHECKPOINT_SPEED  | BIGINT  | Average speed of checkpoint progressing the redo apply point (average speed over the last 3000 seconds) (unit: KB/s) |
| FREE_SPACE_SIZE      | BIGINT  | Remaining space in the redo file (unit: bytes)                            |
| TOTAL_WRITE_SIZE     | BIGINT  | Total size of writes to the redo file since the database started (unit: bytes) |
| BATCH_COMMIT_DELAY   | INTEGER  | Delay time from transaction commit to triggering redo flushing when COMMIT_LOGGING is set to BATCH (unit: us) |