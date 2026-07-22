This view displays the quota information used by the vectorization calculation process PLAN in all nodes of the ISC distributed cluster.

|Field |Type |Description |
|-----------------|-------------|-----------------|
| GROUP\_ID       | INTEGER     | Group ID                                     |
| GROUP\_NODE\_ID | INTEGER     | Node ID within the group                     |
| SQL\_ID         | VARCHAR(13) | Unique identifier for a SQL statement       |
| STAGE\_ID       | INTEGER     | ID of the STAGE in SQL                       |
| PLAN\_ID        | INTEGER     | ID of the PLAN in SQL                        |
| NAME            | VARCHAR(16) | Name of the PLAN in SQL                      |
| ESTIMATE        | BIGINT      | Estimated quota size                         |
| LOW\_LEVEL      | BIGINT      | Quota lower limit                            |
| UP\_LEVEL       | BIGINT      | Quota upper limit                            |
| DEGREE          | SMALLINT    | Degree of parallelism                        |
| MIN\_UP\_LEVEL  | BIGINT      | Minimum quota upper limit for the operator   |
| EXPEND          | BOOLEAN     | Whether the quota can be expanded            |
| MAX\_USED       | BIGINT      | Maximum used quota                           |
| USED            | BIGINT      | Currently used quota                         |