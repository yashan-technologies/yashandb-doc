This view displays the statistics of YAC message interaction.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER   | Group ID                                                                                          |
| GROUP_NODE_ID       | NUMBER   | Node ID within the group                                                                          |
| INST_ID             | NUMBER   | Instance ID                                                                                       |
| ID                  | INTEGER  | Message sequence number                                                                           |
| NAME                | VARCHAR(32) | Message name                                                                                     |
| MESSAGE_GROUP       | VARCHAR(32) | Message group<br>\*   AXC_FG_TASK: Frontend thread<br>\*   AXC_GRC_TASK: GRC thread<br>\*   AXC_GCS_TASK: GCS thread<br>\*   AXC_GLS_TASK: GLS thread<br>\*   AXC_REFORM_TASK: reform thread<br>\*   AXC_BATCH_TASK: batch processing thread<br>\*   AXC_BCST_TASK: DDL class thread<br>\*   AXC_AUX_TASK: auxiliary thread<br>\*   AXC_SYNC_TASK: synchronization thread<br>\*   AXC_SMON_TASK: deadlock detection thread<br>\*   AXC_RCYTEMP_TASK: temporary tablespace recovery thread |
| SEND_TIMES          | BIGINT   | Number of successful message sends                                                                |
| SEND_FAILED_TIMES   | BIGINT   | Number of failed message sends                                                                     |
| SEND_TOTAL_COSTS    | BIGINT   | Total time spent on successful message sends (unit: microseconds)                                 |
| SEND_AVG_COST       | INTEGER  | Average time spent on successful message sends (unit: microseconds)                               |
| SEND_MAX_COST       | INTEGER  | Longest time spent on successful message sends (unit: microseconds)                               |
| RECV_TIMES          | BIGINT   | Number of message receptions                                                                        |
| RECV_TOTAL_COSTS    | BIGINT   | Total time spent on message receptions (unit: microseconds)                                       |
| RECV_AVG_COST       | INTEGER  | Average time spent on message receptions (unit: microseconds)                                     |
| RECV_MAX_COST       | INTEGER  | Longest time spent on message receptions (unit: microseconds)                                     |
| WAIT_TIMES          | BIGINT   | Number of asynchronous messages waiting to be processed                                            |
| WAIT_TOTAL_COSTS    | BIGINT   | Total waiting time for asynchronous messages (unit: microseconds)                                  |
| WAIT_AVG_COST       | INTEGER  | Average waiting time for asynchronous messages (unit: microseconds)                                |
| WAIT_MAX_COST       | INTEGER  | Longest waiting time for asynchronous messages (unit: microseconds)                                |
| PROCESS_TIMES       | BIGINT   | Number of successfully processed messages                                                          |
| PROCESS_FAILED_TIMES | BIGINT   | Number of failed message processes                                                                   |
| PROCESS_TOTAL_COSTS  | BIGINT   | Total time spent on successfully processed messages (unit: microseconds)                           |
| PROCESS_AVG_COST    | INTEGER  | Average time spent on successfully processed messages (unit: microseconds)                         |
| PROCESS_MAX_COST    | INTEGER  | Longest time spent on successfully processed messages (unit: microseconds)                         |