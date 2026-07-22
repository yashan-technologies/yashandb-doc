This view displays the resource status of YAC data page.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE_NAME | VARCHAR(128)| Resource name, block resource [space][file][id]                                     |
| XOWNER        | TINYINT     | The node that holds the lock or the last node that held the lock                    |
| OWNER_COUNT   | TINYINT     | The number of nodes that hold the resource                                           |
| OWNER_MAP     | BIGINT      | The bitmap of nodes holding the resource, a 64-bit integer value, each bit represents the node's ID; if the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN_PROCESS     | BOOLEAN     | Whether any node has requested to acquire the current resource                        |
| REQUEST_COUNT | TINYINT     | The current number of request messages on the resource                                |
| PASTCOPY_MAP  | BIGINT      | The bitmap of nodes holding the PASTCOPY, this field marks the nodes holding the PASTCOPY resource of the BLOCK |
| DISK_LSN      | BIGINT      | The LSN of the last flush                                                          |
| WRITE_INST    | TINYINT     | The instance ID that is currently flushing                                           |
| OBJ           | BIGINT      | The ID of the object to which the current resource belongs                           |
| IS_AFFINITY | BOOLEAN | Whether this resource belongs to instance affinity objects |