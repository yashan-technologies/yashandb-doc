This view shows the quota information used by the SQL in the vectorization calculation process of all nodes in the ISC distributed cluster.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| GROUP_ID            | INTEGER     | Group ID                       |
| GROUP_NODE_ID      | INTEGER     | Node ID within the group       |
| SQL_ID              | VARCHAR(13) | The ID value that uniquely identifies a SQL statement |
| ESTIMATE             | BIGINT      | Estimated quota size           |
| LOW_LEVEL           | BIGINT      | Quota lower limit              |
| UP_LEVEL            | BIGINT      | Quota upper limit              |
| FREE_UP_LEVEL      | BIGINT      | Maximum value of reserved quota |
| MAX_USED            | BIGINT      | Maximum used quota             |
| USED                 | BIGINT      | Current used quota             |