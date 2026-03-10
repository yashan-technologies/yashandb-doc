This view displays the quota information used by the vectorization calculation process STAGE on all nodes in the distributed cluster.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| GROUP_ID             | INTEGER     | Group ID                          |
| GROUP_NODE_ID        | INTEGER     | Node ID within the group          |
| SQL_ID               | VARCHAR(13) | Unique ID value identifying a SQL statement |
| STAGE_ID             | INTEGER     | ID of the STAGE in the SQL       |
| PLAN_ID              | INTEGER     | ID of the PLAN in the SQL        |
| ESTIMATE             | BIGINT      | Estimated quota size              |
| LOW_LEVEL            | BIGINT      | Quota lower limit                 |
| UP_LEVEL             | BIGINT      | Quota upper limit                 |
| FREE_UP_LEVEL        | BIGINT      | Free quota upper limit            |
| PLAN_MIN_UP_LEVEL    | BIGINT      | Minimum quota upper limit for the operator |
| MAX_USED             | BIGINT      | Maximum used quota                |
| USED                 | BIGINT      | Currently used quota              |
| BULK_SIZE            | INTEGER     | Number of rows per batch          |