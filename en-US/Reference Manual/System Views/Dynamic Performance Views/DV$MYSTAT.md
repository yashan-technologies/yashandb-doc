This view displays the statistics of the current sessions of all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | INTEGER  | Group ID                                                                                           |
| GROUP_NODE_ID | INTEGER  | Node ID within the group                                                                           |
| SID           | SMALLINT | Current session ID                                                                                 |
| STATISTIC#    | INTEGER  | Statistic ID, which identifies each statistic. You can find the specific name of the corresponding statistic in the V$STATNAME view using STATISTIC# |
| VALUE         | BIGINT   | Statistic value                                                                                    |