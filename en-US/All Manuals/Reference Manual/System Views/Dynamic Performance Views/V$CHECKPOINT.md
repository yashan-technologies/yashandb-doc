This view displays information related to checkpoints.

|Field |Type |Description |
| --- | --- | --- |
| TOTAL_NUM        | BIGINT     | Total number of checkpoints           |
| SCHEDULE_NUM     | BIGINT     | Number of checkpoints triggered by conditions |
| LAST_EXECUTED    | DATE       | Time of last executed checkpoint      |
| CURRENT_STATUS    | VARCHAR(16)| Current status<br>\* FULL: full<br>\* INCREMENTAL: incremental<br>\* IDLE: idle |
| DIRTY_QUEUE_LENGTH| BIGINT     | Length of the checkpoint dirty block queue |
| DIRTY_QUEUE_FIRST | VARCHAR(32)| First block in the checkpoint dirty block queue |
| DIRTY_QUEUE_LAST  | VARCHAR(32)| Last block in the checkpoint dirty block queue |
| TRUNC_POINT       | VARCHAR(32)| Log truncation point for the checkpoint dirty block queue |