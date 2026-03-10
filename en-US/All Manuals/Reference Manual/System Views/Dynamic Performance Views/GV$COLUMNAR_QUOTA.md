This view shows the quota information globally used in the vectorization calculation process.

|Field |Type |Description |
|--------------------|---------|-------------|
| GROUP_ID            | NUMBER    | Group ID                   |
| GROUP_NODE_ID       | NUMBER    | Node ID within the group   |
| INST_ID             | NUMBER    | Instance ID                |
| TOTAL               | BIGINT    | Total quota size           |
| USED                | BIGINT    | Used quota size            |
| FREE_UP_LEVEL       | BIGINT    | Upper limit of allocated quota |
| ACTIVE_SQL_COUNT     | BIGINT    | Current number of SQL statements |
| MAX_USED            | BIGINT    | Maximum used quota         |