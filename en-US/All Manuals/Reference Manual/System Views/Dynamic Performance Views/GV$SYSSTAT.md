This view displays the relevant statistics of all sessions. 

|Field |Type |Description |
|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------|
| GROUP_ID       | NUMBER      | Group ID                                                                                                                                   |
| GROUP_NODE_ID  | NUMBER      | Node ID within the group                                                                                                                  |
| INST_ID        | NUMBER      | Instance ID                                                                                                                                 |
| STATISTIC#     | INTEGER     | Statistic ID                                                                                                                                 |
| NAME           | VARCHAR(64) | System statistic item name                                                                                                                  |
| CLASS          | INTEGER     | System statistic item category<br>\* 1: User<br>\* 2: Redo<br>\* 4: Enqueue<br>\* 8: Cache<br>\* 16: OS<br>\* 32: Cluster<br>\* 64: SQL<br>\* 128: DEBUG |
| VALUE          | BIGINT      | Statistic value                                                                                                                            |