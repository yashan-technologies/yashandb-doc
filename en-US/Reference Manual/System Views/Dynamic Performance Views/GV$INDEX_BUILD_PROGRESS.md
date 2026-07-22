This view displays the index creation progress information for all indexes within the node.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER       | Group ID                                        |
| GROUP_NODE_ID   | NUMBER       | Node ID within the group                        |
| INST_ID         | NUMBER       | Instance ID                                     |
| TABLE_NAME      | VARCHAR(64)  | Table name                                      |
| INDEX_NAME      | VARCHAR(64)  | Index name                                      |
| INDEX_TYPE      | VARCHAR(64)  | Index type                                      |
| PROGRESS        | INTEGER      | Build progress (0 to 100)                       |
| TIME_USED       | BIGINT       | Time elapsed (unit: milliseconds)               |
| TIME_REMAINING  | BIGINT       | Time remaining (unit: milliseconds)            |
| BUILD_MESSAGE   | VARCHAR(256) | Build message                                   |
