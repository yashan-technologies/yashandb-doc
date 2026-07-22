This view displays the statistics of redo performance.

|Field |Type |Description |
| --- | --- | --- |
| REDO_FLUSH_SPEED         | BIGINT  | Current redo flush speed (average speed over the last 3 seconds) (unit: KB/s) |
| CHECKPOINT_SPEED          | BIGINT  | Current speed of checkpoint advancing redo apply point (average speed over the last 3 seconds) (unit: KB/s) |
| AVR_CHECKPOINT_SPEED      | BIGINT  | Average speed of checkpoint advancing redo apply point (average speed over the last 3000 seconds) (unit: KB/s) |
| FREE_SPACE_SIZE           | BIGINT  | Remaining space of redo files (unit: bytes)                                |
| TOTAL_WRITE_SIZE          | BIGINT  | Total size written to redo files since the database started (unit: bytes)  |
| BATCH_COMMIT_DELAY        | INTEGER | Delay time from transaction commit to triggering redo flush when COMMIT_LOGGING is set to BATCH (unit: us) |