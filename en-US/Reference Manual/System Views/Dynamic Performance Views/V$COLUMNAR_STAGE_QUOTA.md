This view shows the quota information used by the vectorization calculation process STAGE.

|Field |Type |Description |
|----------------------|--------------|-----------------|
| SQL\_ID               | VARCHAR(13)   | The unique ID value that identifies a SQL statement |
| STAGE\_ID             | INTEGER       | The ID of the STAGE in SQL               |
| PLAN\_ID              | INTEGER       | The ID of the PLAN in SQL                 |
| ESTIMATE              | BIGINT        | Estimated quota size                      |
| LOW\_LEVEL            | BIGINT        | Lower limit of the quota                  |
| UP\_LEVEL             | BIGINT        | Upper limit of the quota                  |
| FREE\_UP\_LEVEL       | BIGINT        | Upper limit of free quota                 |
| PLAN\_MIN\_UP\_LEVEL   | BIGINT        | Minimum upper limit of the operator's quota |
| MAX\_USED             | BIGINT        | Maximum used quota                        |
| USED                  | BIGINT        | Currently used quota                       |
| BULK\_SIZE            | INTEGER       | Number of records per batch               |