This view displays information about the BASE section of the MEX memory pool for all nodes.

|Field |Type |Description |
|-----------------------|--------|----------------|
| GROUP_ID                 | NUMBER  | Group ID                           |
| GROUP_NODE_ID            | NUMBER  | Node ID within the group           |
| INST_ID                  | NUMBER  | Instance ID                        |
| HOLD_SIZE               | BIGINT  | Memory held (in bytes)            |
| USING_SIZE              | BIGINT  | Memory in use (in bytes)          |
| UNUSED_SIZE             | BIGINT  | Unused memory (in bytes)          |
| FILL_TIMES              | BIGINT  | Number of fills (buffer miss count)|
| MAX_CONTINUOUS_SIZE    | BIGINT  | Maximum contiguous memory (in bytes)|