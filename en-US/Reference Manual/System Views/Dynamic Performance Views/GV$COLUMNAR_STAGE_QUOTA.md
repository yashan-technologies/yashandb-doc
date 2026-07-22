This view displays the quota information used by the vectorization calculation process STAGE.

|Field |Type |Description |
|----------------------|-------------|-----------------|
| GROUP_ID             | NUMBER      | Group ID                                |
| GROUP_NODE_ID        | NUMBER      | Node ID within the group                |
| INST\_ID             | NUMBER      | Instance ID                             |
| SQL\_ID              | VARCHAR(13) | ID value that uniquely identifies a SQL statement |
| STAGE\_ID            | INTEGER     | ID of the STAGE in SQL                  |
| PLAN\_ID             | INTEGER     | ID of the PLAN in SQL                   |
| ESTIMATE             | BIGINT      | Estimated quota size                    |
| LOW\_LEVEL           | BIGINT      | Quota lower limit                       |
| UP\_LEVEL            | BIGINT      | Quota upper limit                       |
| FREE\_UP\_LEVEL      | BIGINT      | Free quota upper limit                  |
| PLAN\_MIN\_UP\_LEVEL | BIGINT      | Minimum upper limit of the operator's quota |
| MAX\_USED            | BIGINT      | Maximum used quota                      |
| USED                 | BIGINT      | Currently used quota                    |
| BULK_SIZE            | INTEGER     | Number of rows per batch                |