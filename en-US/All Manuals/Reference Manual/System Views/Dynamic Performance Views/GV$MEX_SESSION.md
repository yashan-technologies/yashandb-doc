This view shows the memory usage of MEX for all nodes in various sessions.

|Field |Type |Description |
|---------------------|----------|-------------------|
| GROUP_ID            | NUMBER    | Group ID                             |
| GROUP_NODE_ID       | NUMBER    | Node ID within the group            |
| INST_ID             | NUMBER    | Instance ID                          |
| SID                 | SMALLINT  | Session ID                           |
| MAX_HOLD_SIZE     | BIGINT    | Maximum allowed memory held (in bytes) |
| HOLD_SIZE          | BIGINT    | Held memory (in bytes)              |
| USING_SIZE         | BIGINT    | Memory in use (in bytes)            |
| UNUSED_SIZE        | BIGINT    | Unused memory (in bytes)            |
| TOTAL_ALLOC_TIMES | BIGINT    | Historical allocation count          |
| TOTAL_ALLOC_SIZE  | BIGINT    | Historical allocation size (in bytes)|
| TOTAL_FREE_TIMES  | BIGINT    | Historical release count             |
| TOTAL_FREE_SIZE   | BIGINT    | Historical release size (in bytes)  |
| TOTAL_FILL_TIMES  | BIGINT    | Historical buffer fill count (buffer miss count) |
| TOTAL_GC_TIMES    | BIGINT    | Historical garbage collection count   |