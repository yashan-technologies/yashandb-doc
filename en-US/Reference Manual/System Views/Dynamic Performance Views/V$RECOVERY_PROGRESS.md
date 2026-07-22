This view displays a summary of the log apply progress information.

|Field |Type |Description |
| --- | --- | --- |
| ITEM | VARCHAR(32) | The name of the statistic item |
| UNITS | VARCHAR(16) | The unit of the statistic item |
| VALUE | BIGINT | The value of the statistic item |

Statistic item information:

|Statistic Item |Unit |Description |
| --- | --- | --- |
| Active Apply Rate | KB/sec | Current apply speed |
| Average Apply Rate | KB/sec | Average apply speed |
| Maximum Apply Rate | KB/sec | Maximum apply speed |
| Redo Applied | MB | Amount of logs that have been applied |
| Redo Remain | MB | Amount of logs remaining to be applied. If -1, it indicates that archive log files have a gap |
| First Applied Redo | LFN | LFN number (log flush sequence number) of the first log applied |
| Last Applied Redo | LFN | LFN number (log flush sequence number) of the last log applied |
| Active Time | Seconds | Time consumed for log apply (this time does not increase when there are no logs to apply) |
| Remain Time | Seconds | Estimated remaining apply time for the logs (this will be 0 when redo logs are fully applied) |
| Auxiliary Log Count | Number | Number of auxiliary logs such as DDL during parallel apply; an increase in this item affects parallel apply performance |
| Parallelism | Threads | Number of parallel apply threads; this is 0 when parallel apply is not enabled, can only be a power of 2 when enabled |
| Latest Load Size | KB | Size of the most recent log read |
| Latest Load Time | Millisecond | Time taken for the most recent log read |
| Latest Redo Merge Time | Millisecond |    Time taken for the most recent log merge (when a DDL log is encountered, the DDL will be replayed during the log merge, which will make the time taken larger) |
| Latest Analysis Time | Millisecond | Time taken for the most recent log analysis |
| Wait Previous Apply Time | Millisecond | Time spent waiting for the previous apply to finish |
| Latest Apply Time | Millisecond | Total time for the most recent log apply |

During the transition from MOUNT to OPEN, the statistics reflect the restart apply information, and the Redo Remain item in the view will decrease as apply progresses. After the primary database is OPEN, the view items will no longer change. However, after the standby database is OPEN, the view content may be reset, and the Redo Remain and Remain Time items will indicate the size and apply time corresponding to the current remaining logs.