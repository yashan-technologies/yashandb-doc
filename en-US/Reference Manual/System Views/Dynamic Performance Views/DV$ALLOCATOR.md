This view shows the memory usage status of all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| NAME | VARCHAR(64) | Memory type |
| TOTAL\_MEMORY | BIGINT | Total allocated memory size (in bytes) |
| CURR\_MEMORY\_USED | BIGINT | Current total memory used (in bytes) |
| FREE\_MEMORY | BIGINT | Current remaining memory size (in bytes) |
| MAX\_MEMORY\_USED | BIGINT | Peak memory used during this node's runtime (in bytes) |