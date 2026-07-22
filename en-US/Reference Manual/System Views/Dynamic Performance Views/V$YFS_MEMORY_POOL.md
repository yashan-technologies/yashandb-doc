This view displays the YFS memory pool (MEMORY POOL) information for the current node.

This is only applicable to YAC/Distrubuted Cluster Deployment; this view is meaningless in other deployment types.

|Field |Type |Description |
|----------------|-------------|-----------------------------------------------|
| NAME           | VARCHAR(68) | The name of the memory pool                    |
| TOTAL_SIZE     | BIGINT      | The total size of the memory pool (unit: bytes) |
| USED_SIZE      | BIGINT      | The amount of space used in the memory pool (unit: bytes) |
| FREE_SIZE      | BIGINT      | The amount of unused space in the memory pool (unit: bytes) |
| MAX_SIZE       | BIGINT      | The maximum size of the memory pool (unit: bytes) |