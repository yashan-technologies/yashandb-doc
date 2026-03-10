This view shows the global resource status of YACs.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER   | Group ID                                                                                          |
| GROUP_NODE_ID  | NUMBER   | Node ID within the group                                                                          |
| INST_ID        | NUMBER   | Instance ID                                                                                       |
| RESOURCE_NAME  | VARCHAR(272) | Resource name, block resource [space][file][id], lock resource [id][type], gtid resource [len][string]  |
| TYPE           | INTEGER  | Resource type<br>\*   0: BLOCK, data page<br>\*   1: LOCK, lock resource<br>\*   2: GTID, global transaction ID resource |
| XOWNER         | TINYINT  | The node that holds the write lock or the node that most recently held the write lock            |
| OWNER_COUNT    | TINYINT  | Number of nodes holding the resource                                                               |
| OWNER_MAP      | BIGINT   | Bitmap of nodes holding the resource, a 64-bit integer value, where each bit represents the ID of a node; if the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN_PROCESS      | BOOLEAN  | Whether any node is requesting to acquire the current resource                                     |
| REQUEST_COUNT   | TINYINT  | The current number of request messages on the resource                                            |