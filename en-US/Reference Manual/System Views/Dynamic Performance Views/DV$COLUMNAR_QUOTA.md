This view displays the quota information globally used in the vectorization calculation process for all nodes in an ISC distributed cluster.

|Field |Type |Description |
|--------------------|---------|-------------|
| GROUP\_ID          | INTEGER  | Group ID            |
| GROUP\_NODE\_ID    | INTEGER  | Node ID within group |
| TOTAL              | BIGINT   | Total quota size     |
| USED               | BIGINT   | Used quota size      |
| FREE\_UP\_LEVEL    | BIGINT   | Limit of allocated quota |
| ACTIVE\_SQL\_COUNT | BIGINT   | Current number of SQL statements |
| MAX\_USED          | BIGINT   | Maximum used quota    |