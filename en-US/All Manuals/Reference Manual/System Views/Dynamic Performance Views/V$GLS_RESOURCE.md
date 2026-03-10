This view displays the global lock resource status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE_NAME  | VARCHAR(128) | Resource name, lock resource \[id\]\[type\]                |
| XOWNER           | TINYINT     | The node that holds the write lock or the node that last held the write lock |
| OWNER_COUNT    | TINYINT     | The number of nodes holding the resource                     |
| OWNER_MAP      | BIGINT      | Bitmap of nodes holding the resource, a 64-bit integer value, each bit represents a node's ID; if the node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN_PROCESS      | BOOLEAN     | Whether there are nodes requesting to acquire the current resource |
| REQUEST_COUNT   | TINYINT     | The current number of request messages on the resource       |
| UNLOCK_MAP     | BIGINT      | Bitmap of nodes holding this lock resource                   |