This view displays the data page resource situation in YAC.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER    | Group ID                                                            |
| GROUP_NODE_ID   | NUMBER    | Node ID within the group                                            |
| INST_ID         | NUMBER    | Instance ID                                                         |
| RESOURCE_NAME   | VARCHAR(128) | Resource name, block resource [space][file][id]                  |
| XOWNER          | TINYINT   | The node that holds the lock or the node that held the lock most recently |
| OWNER_COUNT     | TINYINT   | The number of nodes holding the resource                             |
| OWNER_MAP       | BIGINT    | Bitmap of nodes holding the resource, a 64-bit integer value, where each bit represents the ID of a node; if the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN_PROCESS       | BOOLEAN   | Whether there are nodes requesting to acquire the current resource    |
| REQUEST_COUNT    | TINYINT   | The current number of request messages for the resource              |
| PASTCOPY_MAP    | BIGINT    | Bitmap of nodes holding PASTCOPY, this field marks nodes that hold PASTCOPY resources for this BLOCK |
| DISK_LSN        | BIGINT    | The LSN of the last flush operation                                  |
| WRITE_INST      | TINYINT   | The instance ID currently flushing                                   |
| OBJ             | BIGINT    | The object ID to which the current resource belongs                  |
| IS_AFFINITY | BOOLEAN | Whether this resource belongs to instance affinity objects |