This view displays the quota information used in the vectorization calculation process PLAN.

|Field |Type |Description |
|----------------|-------------|-----------------|
| GROUP_ID       | NUMBER      | Group ID                            |
| GROUP_NODE_ID  | NUMBER      | Node ID within the group           |
| INST_ID        | NUMBER      | Instance ID                         |
| SQL_ID         | VARCHAR(13) | ID value uniquely identifying a SQL statement |
| STAGE_ID       | INTEGER     | ID of the STAGE in SQL             |
| PLAN_ID        | INTEGER     | ID of the PLAN in SQL              |
| NAME           | VARCHAR(16) | Name of the PLAN in SQL            |
| ESTIMATE       | BIGINT      | Estimated quota size                |
| LOW_LEVEL      | BIGINT      | Lower limit of the quota           |
| UP_LEVEL       | BIGINT      | Upper limit of the quota           |
| DEGREE         | SMALLINT    | Degree of parallelism              |
| MIN_UP_LEVEL   | BIGINT      | Minimum upper limit of the operator's quota |
| EXPEND         | BOOLEAN     | Whether quota expansion is allowed  |
| MAX_USED       | BIGINT      | Maximum used quota                  |
| USED           | BIGINT      | Currently used quota                |