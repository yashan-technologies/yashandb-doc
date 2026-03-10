This view displays statistics of redo performance for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| REDO_FLUSH_SPEED  | BIGINT | Current redo flush speed (average speed over the last 3 seconds) (unit: KB/s) |
| CHECKPOINT_SPEED  | BIGINT | Current speed of checkpoint advancing the redo apply point (average speed over the last 3 seconds) (unit: KB/s) |
| AVR_CHECKPOINT_SPEED  | BIGINT | Average speed of checkpoint advancing the redo apply point (average speed over the last 3000 seconds) (unit: KB/s) |
| FREE_SPACE_SIZE  | BIGINT | Remaining space in the redo file (unit: bytes) |
| TOTAL_WRITE_SIZE  | BIGINT | Total size of redo file writes since the database started (unit: bytes) |
| BATCH_COMMIT_DELAY  | INTEGER | Delay time for transaction commits to trigger redo flush when the COMMIT_LOGGING parameter is set to BATCH (unit: us) |