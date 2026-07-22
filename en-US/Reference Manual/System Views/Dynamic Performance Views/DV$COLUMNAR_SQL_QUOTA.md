This view shows the quota information used by the SQL in the vectorization calculation process of all nodes in the ISC distributed cluster.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| GROUP\_ID            | INTEGER     | Group ID                       |
| GROUP\_NODE\_ID      | INTEGER     | Node ID within the group       |
| SQL\_ID              | VARCHAR(13) | The ID value that uniquely identifies a SQL statement |
| ESTIMATE             | BIGINT      | Estimated quota size           |
| LOW\_LEVEL           | BIGINT      | Quota lower limit              |
| UP\_LEVEL            | BIGINT      | Quota upper limit              |
| FREE\_UP\_LEVEL      | BIGINT      | Maximum value of reserved quota |
| MAX\_USED            | BIGINT      | Maximum used quota             |
| USED                 | BIGINT      | Current used quota             |