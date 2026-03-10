This view displays the statistics of YAC message interaction.

|Field |Type |Description |
| --- | --- | --- |
| ID | INTEGER | Message sequence number |
| NAME | VARCHAR(32) | Message name |
| MESSAGE_GROUP | VARCHAR(32) | Message group<br>\* AXC_FG_TASK: Front-end thread<br>\* AXC_GRC_TASK: GRC thread<br>\* AXC_GCS_TASK: GCS thread<br>\* AXC_GLS_TASK: GLS thread<br>\* AXC_REFORM_TASK: Reform thread<br>\* AXC_BATCH_TASK: Batch processing thread<br>\* AXC_BCST_TASK: DDL type thread<br>\* AXC_AUX_TASK: Auxiliary thread<br>\* AXC_SYNC_TASK: Synchronization thread<br>\* AXC_SMON_TASK: Deadlock detection thread<br>\* AXC_RCYTEMP_TASK: Temporary table space recovery thread |
| SEND_TIMES | BIGINT | Number of successful message sends |
| SEND_FAILED_TIMES | BIGINT | Number of failed message sends |
| SEND_TOTAL_COSTS | BIGINT | Total time spent on successful message sends (unit: microseconds) |
| SEND_AVG_COST | INTEGER | Average time spent on successful message sends (unit: microseconds) |
| SEND_MAX_COST | INTEGER | Maximum time spent on successful message sends (unit: microseconds) |
| RECV_TIMES | BIGINT | Number of message receipts |
| RECV_TOTAL_COSTS | BIGINT | Total time spent on message receipts (unit: microseconds) |
| RECV_AVG_COST | INTEGER | Average time spent on message receipts (unit: microseconds) |
| RECV_MAX_COST | INTEGER | Maximum time spent on message receipts (unit: microseconds) |
| WAIT_TIMES | BIGINT | Number of times asynchronous messages waited for processing |
| WAIT_TOTAL_COSTS | BIGINT | Total time spent queuing asynchronous messages (unit: microseconds) |
| WAIT_AVG_COST | INTEGER | Average time spent queuing asynchronous messages (unit: microseconds) |
| WAIT_MAX_COST | INTEGER | Maximum time spent queuing asynchronous messages (unit: microseconds) |
| PROCESS_TIMES | BIGINT | Number of successful message processes |
| PROCESS_FAILED_TIMES | BIGINT | Number of failed message processes |
| PROCESS_TOTAL_COSTS | BIGINT | Total time spent on successful message processes (unit: microseconds) |
| PROCESS_AVG_COST | INTEGER | Average time spent on successful message processes (unit: microseconds) |
| PROCESS_MAX_COST | INTEGER | Maximum time spent on successful message processes (unit: microseconds) |