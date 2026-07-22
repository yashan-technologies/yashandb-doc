This view shows the memory usage of MEX for all nodes in various sessions.

|Field |Type |Description |
|---------------------|----------|-------------------|
| GROUP_ID            | NUMBER    | Group ID                             |
| GROUP_NODE_ID       | NUMBER    | Node ID within the group            |
| INST_ID             | NUMBER    | Instance ID                          |
| SID                 | SMALLINT  | Session ID                           |
| MAX\_HOLD\_SIZE     | BIGINT    | Maximum allowed memory held (in bytes) |
| HOLD\_SIZE          | BIGINT    | Held memory (in bytes)              |
| USING\_SIZE         | BIGINT    | Memory in use (in bytes)            |
| UNUSED\_SIZE        | BIGINT    | Unused memory (in bytes)            |
| TOTAL\_ALLOC\_TIMES | BIGINT    | Historical allocation count          |
| TOTAL\_ALLOC\_SIZE  | BIGINT    | Historical allocation size (in bytes)|
| TOTAL\_FREE\_TIMES  | BIGINT    | Historical release count             |
| TOTAL\_FREE\_SIZE   | BIGINT    | Historical release size (in bytes)  |
| TOTAL\_FILL\_TIMES  | BIGINT    | Historical buffer fill count (buffer miss count) |
| TOTAL\_GC\_TIMES    | BIGINT    | Historical garbage collection count   |