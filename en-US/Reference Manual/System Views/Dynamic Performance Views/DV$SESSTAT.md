This view displays the statistics of all sessions in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | INTEGER   | Group ID                                                                                   |
| GROUP_NODE_ID | INTEGER   | Node ID within the group                                                                    |
| SID           | SMALLINT  | Session ID                                                                                |
| STATISTIC#    | INTEGER   | Statistic ID, identifying each statistic. The specific name of the statistic can be found in the V$STATNAME view using STATISTIC#. |
| VALUE         | BIGINT    | Statistic value                                                                            |