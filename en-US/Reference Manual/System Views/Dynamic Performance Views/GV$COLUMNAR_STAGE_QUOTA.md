This view displays the quota information used by the vectorization calculation process STAGE.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| GROUP_ID             | NUMBER      | Group ID                                |
| GROUP_NODE_ID        | NUMBER      | Node ID within the group                |
| INST_ID             | NUMBER      | Instance ID                             |
| SQL_ID              | VARCHAR(13) | ID value that uniquely identifies a SQL statement |
| STAGE_ID            | INTEGER     | ID of the STAGE in SQL                  |
| PLAN_ID             | INTEGER     | ID of the PLAN in SQL                   |
| ESTIMATE             | BIGINT      | Estimated quota size                    |
| LOW_LEVEL           | BIGINT      | Quota lower limit                       |
| UP_LEVEL            | BIGINT      | Quota upper limit                       |
| FREE_UP_LEVEL      | BIGINT      | Free quota upper limit                  |
| PLAN_MIN_UP_LEVEL | BIGINT      | Minimum upper limit of the operator's quota |
| MAX_USED            | BIGINT      | Maximum used quota                      |
| USED                 | BIGINT      | Currently used quota                    |
| BULK_SIZE            | INTEGER     | Number of rows per batch                |