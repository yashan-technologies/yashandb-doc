This view shows the status of YAC data lock resources.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER  | Group ID                                                     |
| GROUP_NODE_ID | NUMBER  | Node ID within the group                                     |
| INST_ID       | NUMBER  | Instance ID                                                  |
| RESOURCE_NAME  | VARCHAR(128) | Resource name, lock resource [id][type]                   |
| XOWNER        | TINYINT | Node holding the write lock or the node that most recently held the write lock |
| OWNER_COUNT   | TINYINT | Number of nodes holding the resource                          |
| OWNER_MAP     | BIGINT  | Bitmap of nodes holding the resource, a 64-bit integer value, each bit represents a node ID. If the node holds the resource, the corresponding bit in ownerMap is set to 1. |
| IN_PROCESS    | BOOLEAN | Whether any node has requested to acquire the current resource |
| REQUEST_COUNT | TINYINT | Current number of request messages on the resource           |
| UNLOCK_MAP    | BIGINT  | Bitmap of nodes holding the lock resource                    |