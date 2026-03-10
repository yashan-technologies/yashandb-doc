This view displays information about statistics items, corresponding to the statistics items in V$SYSSTAT.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER  | Group ID                   |
| GROUP_NODE_ID| NUMBER  | Node ID within the group    |
| INST_ID      | NUMBER  | Instance ID                |
| STATISTIC#   | INTEGER | Statistic ID               |
| NAME         | VARCHAR(64) | Name of the system statistic |
| CLASS        | INTEGER | Class of the system statistic<br>*   1: User<br>*   2: Redo<br>*   4: Enqueue<br>*   8: Cache<br>*   16: OS<br>*   32: Cluster<br>*   64: SQL<br>*   128: DEBUG |