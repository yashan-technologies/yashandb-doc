This view shows the global transaction ID resource status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | NUMBER    | Group ID                                                                    |
| GROUP_NODE_ID     | NUMBER    | Node ID within the group                                                    |
| INST_ID           | NUMBER    | Instance ID                                                                 |
| RESOURCE_NAME     | VARCHAR(272) | Resource name, gtid resource [len] [string]                              |
| XOWNER            | TINYINT   | The node holding the write lock or the node that most recently held the write lock |
| OWNER_COUNT       | TINYINT   | Number of nodes holding the resource                                         |
| OWNER_MAP         | BIGINT    | Bitmap of nodes holding the resource, a 64-bit integer value, where each bit represents a node's ID. If the node holds the resource, the corresponding bit in ownerMap is set to 1. |
| IN_PROCESS        | BOOLEAN   | Whether any node has requested to acquire the current resource               |
| REQUEST_COUNT     | TINYINT   | Current number of request messages on the resource                          |
| START_SESSION_ID  | SMALLINT  | Session ID that started the transaction                                      |
| FINISH_SESSION_ID | SMALLINT  | Session ID that finished the transaction                                     |
| FINISH_INSTANCE_ID | TINYINT   | Instance ID that finished the transaction                                     |