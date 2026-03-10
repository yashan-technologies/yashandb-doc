This view displays system utilization statistics from the operating system.

|Field |Type |Description |
| --- | --- | --- |
| STAT_NAME     | VARCHAR(64) | Statistic name                                            |
| VALUE         | BIGINT     | Instantaneous statistic value                             |
| OSSTAT_ID     | INTEGER    | Statistic identifier                                      |
| COMMENTS      | VARCHAR(64) | Any additional operating system-specific notes on the statistics |
| CUMULATIVE    | VARCHAR(3) | Indicates whether the statistic is cumulative (i.e., accumulated over time)<br/>* YES<br/>* NO |