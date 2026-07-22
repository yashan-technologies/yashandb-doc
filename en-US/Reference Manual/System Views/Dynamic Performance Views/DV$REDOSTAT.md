This view displays statistics of redo performance for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| REDO\_FLUSH\_SPEED  | BIGINT | Current redo flush speed (average speed over the last 3 seconds) (unit: KB/s) |
| CHECKPOINT\_SPEED  | BIGINT | Current speed of checkpoint advancing the redo apply point (average speed over the last 3 seconds) (unit: KB/s) |
| AVR\_CHECKPOINT\_SPEED  | BIGINT | Average speed of checkpoint advancing the redo apply point (average speed over the last 3000 seconds) (unit: KB/s) |
| FREE\_SPACE\_SIZE  | BIGINT | Remaining space in the redo file (unit: bytes) |
| TOTAL\_WRITE\_SIZE  | BIGINT | Total size of redo file writes since the database started (unit: bytes) |
| BATCH\_COMMIT\_DELAY  | INTEGER | Delay time for transaction commits to trigger redo flush when the COMMIT_LOGGING parameter is set to BATCH (unit: us) |