This view displays the summary information of the log apply progress.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER   | Group ID                                    |
| GROUP_NODE_ID  | NUMBER   | Node ID within the group                    |
| INST_ID        | NUMBER   | Instance ID                                 |
| ITEM           | VARCHAR(32) | Name of the statistic item                 |
| UNITS          | VARCHAR(16) | Unit of the statistic item                 |
| VALUE          | BIGINT   | Value of the statistic item                 |

Statistic item information:

|Statistic Item |Unit |Description |
| --- | --- | --- |
| Active Apply Rate         | KB/sec    | Current apply speed                           |
| Average Apply Rate        | KB/sec    | Average apply speed                           |
| Maximum Apply Rate        | KB/sec    | Maximum apply speed                           |
| Redo Applied              | MB        | Volume of logs that have been applied        |
| Redo Remain               | MB        | Volume of logs that need to be applied; if -1, it indicates that there is a gap in the archive log files |
| First Applied Redo        | LFN       | LFN number of the first log applied (log flush sequence number) |
| Last Applied Redo         | LFN       | LFN number of the last log applied (log flush sequence number) |
| Active Time               | Seconds   | Time spent on log apply (this time does not increase when there are no logs to apply) |
| Remain Time               | Seconds   | Estimated time needed to apply remaining logs (this will be 0 when redo log apply is complete) |
| Auxiliary Log Count       | Number    | Number of auxiliary logs, such as DDL, during parallel apply; an increase in this item will affect parallel apply performance |
| Parallelism               | Threads   | Number of parallel apply threads; value is 0 when parallel apply is not enabled, and it can only be a power of 2 when enabled |
| Latest Load Size          | KB        | Size of the most recent log read             |
| Latest Load Time          | Millisecond | Time taken for the most recent log read    |
| Latest Analysis Time      | Millisecond | Time taken for the most recent log analysis |
| Wait Previous Apply Time   | Millisecond | Time spent waiting for the previous apply to end |
| Latest Apply Time         | Millisecond | Total time for the most recent log apply     |

During the database's transition from MOUNT to OPEN phase, reboot apply information is logged, and the Redo Remain item in the view decreases with each apply. After the primary database is OPEN, the view items no longer change. However, after the standby database is OPEN, the view content may be reset, and the Redo Remain and Remain Time items will represent the current size and apply time of the remaining logs.