This view shows the quota information used by the vectorization calculation process STAGE.

|Field |Type |Description |
|----------------------|--------------|-----------------|
| SQL_ID               | VARCHAR(13)   | The unique ID value that identifies a SQL statement |
| STAGE_ID             | INTEGER       | The ID of the STAGE in SQL               |
| PLAN_ID              | INTEGER       | The ID of the PLAN in SQL                 |
| ESTIMATE              | BIGINT        | Estimated quota size                      |
| LOW_LEVEL            | BIGINT        | Lower limit of the quota                  |
| UP_LEVEL             | BIGINT        | Upper limit of the quota                  |
| FREE_UP_LEVEL       | BIGINT        | Upper limit of free quota                 |
| PLAN_MIN_UP_LEVEL   | BIGINT        | Minimum upper limit of the operator's quota |
| MAX_USED             | BIGINT        | Maximum used quota                        |
| USED                  | BIGINT        | Currently used quota                       |
| BULK_SIZE            | INTEGER       | Number of records per batch               |