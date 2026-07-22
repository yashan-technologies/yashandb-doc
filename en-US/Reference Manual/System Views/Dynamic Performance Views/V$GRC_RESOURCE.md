This view shows the global resource status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| RESOURCE\_NAME | VARCHAR(272) | Resource name, block resource \[space\]\[file\]\[id\], lock resource \[id\]\[type\], gtid resource \[len\]\[string\]  |
| TYPE | INTEGER | Resource type<br>\*   0: BLOCK, data page<br>\*   1: LOCK, lock resource<br>\*   2: GTID, global transaction ID resource  |
| XOWNER | TINYINT | The node that holds the write lock or the most recent node that held the write lock |
| OWNER\_COUNT | TINYINT | The number of nodes holding the resource |
| OWNER\_MAP | BIGINT | A bitmap of nodes holding the resource, a 64-bit integer where each bit represents the ID of a node; if a node holds the resource, the corresponding bit in ownerMap is set to 1 |
| IN\_PROCESS | BOOLEAN | Whether there are nodes requesting to acquire the current resource |
| REQUEST\_COUNT | TINYINT | The current number of request messages on the resource |