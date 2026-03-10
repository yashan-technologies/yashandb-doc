This view displays the quota information used in the vectorization calculation process of SQL.

|Field |Type |Description |
|-----------------|-------------|-----------------|
| GROUP_ID        | NUMBER       | Group ID                              |
| GROUP_NODE_ID   | NUMBER       | Node ID within the group              |
| INST_ID         | NUMBER       | Instance ID                           |
| SQL_ID          | VARCHAR(13)  | Unique ID value identifying a SQL statement |
| ESTIMATE        | BIGINT       | Estimated quota size                  |
| LOW_LEVEL       | BIGINT       | Quota lower limit                     |
| UP_LEVEL        | BIGINT       | Quota upper limit                     |
| FREE_UP_LEVEL   | BIGINT       | Maximum reserved quota value          |
| MAX_USED        | BIGINT       | Maximum used quota                    |
| USED            | BIGINT       | Current used quota                    |