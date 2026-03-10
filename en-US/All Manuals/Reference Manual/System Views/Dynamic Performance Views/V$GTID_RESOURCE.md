This view displays the global transaction ID resource status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE_NAME   | VARCHAR(272) | Resource name, gtid resource \[len\]\[string\]                                         |
| XOWNER           | TINYINT   | The node holding the write lock or the node that last held the write lock               |
| OWNER_COUNT     | TINYINT   | The number of nodes holding the resource                                                |
| OWNER_MAP       | BIGINT    | A bitmap of nodes holding the resource, a 64-bit integer value, where each bit represents the ID of a node. If the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN_PROCESS       | BOOLEAN   | Whether there is a node requesting to acquire the current resource                       |
| REQUEST_COUNT    | TINYINT   | The current number of request messages on the resource                                   |
| START_SESSION_ID | SMALLINT | The session ID that started the transaction                                              |
| FINISH_SESSION_ID | SMALLINT | The session ID that finished the transaction                                             |
| FINISH_INSTANCE_ID | TINYINT | The instance ID that finished the transaction                                            |