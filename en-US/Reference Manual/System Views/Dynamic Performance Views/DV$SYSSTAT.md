This view displays the relevant statistics of all sessions across all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| STATISTIC# | INTEGER | Statistic number |
| NAME | VARCHAR(64) | Name of the system statistic item, which can be fully obtained by querying the V$SYSSTAT view |
| CLASS | INTEGER | System statistic item category<br>\*   1: User<br>\*   2: Redo<br>\*   4: Enqueue<br>\*   8: Cache<br>\*   16: OS<br>\*   32: Cluster<br>\*   64: SQL<br>\*   128: DEBUG |
| VALUE | BIGINT | Statistic value |