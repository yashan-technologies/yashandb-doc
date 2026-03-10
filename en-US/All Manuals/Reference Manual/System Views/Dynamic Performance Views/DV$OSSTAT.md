This view displays the system utilization statistics from the operating system across all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER | Group ID                                       |
| GROUP_NODE_ID     | INTEGER | Node ID within the group                       |
| STAT_NAME         | VARCHAR(64) | Name of the statistic                        |
| VALUE             | BIGINT  | Instantaneous statistic value                   |
| OSSTAT_ID         | INTEGER | Statistic number                                |
| COMMENTS          | VARCHAR(64) | Any additional operating system-specific notes on the statistics |
| CUMULATIVE        | VARCHAR(3)  | Indicates whether the statistic is cumulative (i.e., accumulated over time)<br>\* YES<br>\* NO |