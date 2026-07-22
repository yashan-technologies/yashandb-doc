This view displays the global transaction ID resource status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE\_NAME   | VARCHAR(272) | Resource name, gtid resource \[len\]\[string\]                                         |
| XOWNER           | TINYINT   | The node holding the write lock or the node that last held the write lock               |
| OWNER\_COUNT     | TINYINT   | The number of nodes holding the resource                                                |
| OWNER\_MAP       | BIGINT    | A bitmap of nodes holding the resource, a 64-bit integer value, where each bit represents the ID of a node. If the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN\_PROCESS       | BOOLEAN   | Whether there is a node requesting to acquire the current resource                       |
| REQUEST\_COUNT    | TINYINT   | The current number of request messages on the resource                                   |
| START\_SESSION\_ID | SMALLINT | The session ID that started the transaction                                              |
| FINISH\_SESSION\_ID | SMALLINT | The session ID that finished the transaction                                             |
| FINISH\_INSTANCE\_ID | TINYINT | The instance ID that finished the transaction                                            |