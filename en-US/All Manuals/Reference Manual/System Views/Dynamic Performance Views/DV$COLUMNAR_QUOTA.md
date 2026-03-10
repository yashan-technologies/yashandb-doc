This view displays the quota information globally used in the vectorization calculation process for all nodes in an ISC distributed cluster.

|Field |Type |Description |
|--------------------|---------|-------------|
| GROUP_ID          | INTEGER  | Group ID            |
| GROUP_NODE_ID    | INTEGER  | Node ID within group |
| TOTAL              | BIGINT   | Total quota size     |
| USED               | BIGINT   | Used quota size      |
| FREE_UP_LEVEL    | BIGINT   | Limit of allocated quota |
| ACTIVE_SQL_COUNT | BIGINT   | Current number of SQL statements |
| MAX_USED          | BIGINT   | Maximum used quota    |