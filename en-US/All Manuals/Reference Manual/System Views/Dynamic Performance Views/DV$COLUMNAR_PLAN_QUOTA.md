This view displays the quota information used by the vectorization calculation process PLAN in all nodes of the ISC distributed cluster.

|Field |Type |Description |
|-----------------|-------------|-----------------|
| GROUP_ID       | INTEGER     | Group ID                                     |
| GROUP_NODE_ID | INTEGER     | Node ID within the group                     |
| SQL_ID         | VARCHAR(13) | Unique identifier for a SQL statement       |
| STAGE_ID       | INTEGER     | ID of the STAGE in SQL                       |
| PLAN_ID        | INTEGER     | ID of the PLAN in SQL                        |
| NAME            | VARCHAR(16) | Name of the PLAN in SQL                      |
| ESTIMATE        | BIGINT      | Estimated quota size                         |
| LOW_LEVEL      | BIGINT      | Quota lower limit                            |
| UP_LEVEL       | BIGINT      | Quota upper limit                            |
| DEGREE          | SMALLINT    | Degree of parallelism                        |
| MIN_UP_LEVEL  | BIGINT      | Minimum quota upper limit for the operator   |
| EXPEND          | BOOLEAN     | Whether the quota can be expanded            |
| MAX_USED       | BIGINT      | Maximum used quota                           |
| USED            | BIGINT      | Currently used quota                         |