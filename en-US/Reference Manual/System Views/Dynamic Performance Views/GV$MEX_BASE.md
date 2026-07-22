This view displays information about the memory base address pool in the MEX pool on all nodes.

|Field |Type |Description |
|-----------------------|--------|----------------|
| GROUP_ID                 | NUMBER  | Group ID                           |
| GROUP_NODE_ID            | NUMBER  | Node ID within the group           |
| INST_ID                  | NUMBER  | Instance ID                        |
|  HOLD\_SIZE            | BIGINT | Capacity of memory currently held (in bytes)          |
| USING\_SIZE           | BIGINT | Capacity of memory has been allocated (in bytes)        |
| UNUSED\_SIZE          | BIGINT | Capacity of free memory (in bytes)        |
| FILL\_TIMES           | BIGINT | Total number of cache misses across all areas |
| MAX\_CONTINUOUS\_SIZE | BIGINT | Capacity of the largest contiguous block of memory (in bytes)  |
