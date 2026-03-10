This view displays the system utilization statistics from the operating system for all instances in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER   | Group ID                                                      |
| GROUP_NODE_ID | NUMBER   | Node ID within the group                                      |
| INST_ID       | NUMBER   | Instance ID                                                   |
| STAT_NAME     | VARCHAR(64) | Statistical name                                           |
| VALUE         | BIGINT   | Instantaneous statistical value                                |
| OSSTAT_ID     | INTEGER  | Statistical number                                            |
| COMMENTS      | VARCHAR(64) | Any other OS-specific remarks on the statistical data      |
| CUMULATIVE    | VARCHAR(3) | Indicates whether the statistic is cumulative (i.e., accumulated over time)<br/>* YES<br/>* NO |