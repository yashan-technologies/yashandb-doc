This view displays information related to checkpoints.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER  | Group ID                                   |
| GROUP_NODE_ID       | NUMBER  | Node ID within the group                   |
| INST_ID             | NUMBER  | Instance ID                                |
| TOTAL_NUM           | BIGINT  | Total number of checkpoints                |
| SCHEDULE_NUM        | BIGINT  | Number of checkpoints triggered by conditions |
| LAST_EXECUTED       | DATE    | Time of the last executed checkpoint       |
| CURRENT_STATUS      | VARCHAR(16) | Current status<br>\* FULL: Full<br>\* INCREMENTAL: Incremental<br>\* IDLE: Idle |
| DIRTY_QUEUE_LENGTH  | BIGINT  | Length of the dirty block queue for checkpoints |
| DIRTY_QUEUE_FIRST   | VARCHAR(32) | First block in the dirty block queue for checkpoints |
| DIRTY_QUEUE_LAST    | VARCHAR(32) | Last block in the dirty block queue for checkpoints |
| TRUNC_POINT         | VARCHAR(32) | Truncation point of the checkpoint dirty block queue |