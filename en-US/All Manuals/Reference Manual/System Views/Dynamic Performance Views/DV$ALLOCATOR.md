This view shows the memory usage status of all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| NAME | VARCHAR(64) | Memory type |
| TOTAL_MEMORY | BIGINT | Total allocated memory size (in bytes) |
| CURR_MEMORY_USED | BIGINT | Current total memory used (in bytes) |
| FREE_MEMORY | BIGINT | Current remaining memory size (in bytes) |
| MAX_MEMORY_USED | BIGINT | Peak memory used during this node's runtime (in bytes) |