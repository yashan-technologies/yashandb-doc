This view displays the statistics of all current sessions for the instance.

Difference from V$SYSSTAT: V$SYSSTAT records the cumulative values for all sessions, while V$SESSTAT records the statistics by session ID.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER   | Group ID                                                                    |
| GROUP_NODE_ID| NUMBER   | Node ID within the group                                                    |
| INST_ID      | NUMBER   | Instance ID                                                                 |
| SID          | SMALLINT | Session ID                                                                  |
| STATISTIC#   | INTEGER  | Statistic ID, identifies each statistic item, which can be found in the V$STATNAME view using STATISTIC# to look up the specific name of the statistic |
| VALUE        | BIGINT   | Statistic value                                                             |