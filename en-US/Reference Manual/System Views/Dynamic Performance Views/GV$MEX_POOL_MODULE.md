This view displays information about each functionality module in the MEX memory pool for all nodes.

|Field |Type |Description |
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | Group ID                       |
| GROUP_NODE_ID       | NUMBER      | Node ID within the group       |
| INST_ID             | NUMBER      | Instance ID                    |
| TAG_ID              | INTEGER     | Module Tag ID                  |
| NAME                | VARCHAR(64) | Module Tag Name                |
| TOTAL_ALLOC_TIMES   | BIGINT      | Historical allocation count     |
| TOTAL_ALLOC_SIZE    | BIGINT      | Historical allocation size (in bytes) |
| TOTAL_FREE_TIMES    | BIGINT      | Historical deallocation count   |
| TOTAL_FREE_SIZE     | BIGINT      | Historical deallocation size (in bytes) |