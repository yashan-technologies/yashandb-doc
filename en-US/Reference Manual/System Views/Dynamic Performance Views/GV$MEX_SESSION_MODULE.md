This view shows the usage of various functionality modules in the MEX memory pool across all sessions of each node.

|Field |Type |Description |
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | Group ID                        |
| GROUP_NODE_ID       | NUMBER      | Node ID within the group         |
| INST_ID             | NUMBER      | Instance ID                     |
| SID                 | SMALLINT    | Session ID                      |
| TAG_ID             | INTEGER     | Module Tag ID                   |
| NAME                | VARCHAR(64) | Module Tag Name                 |
| USING_SIZE         | BIGINT      | Memory in use (unit: bytes)    |
| TOTAL_ALLOC_TIMES | BIGINT      | Total allocation count           |
| TOTAL_ALLOC_SIZE  | BIGINT      | Total allocation size (unit: bytes) |
| TOTAL_FREE_TIMES  | BIGINT      | Total free count                 |
| TOTAL_FREE_SIZE   | BIGINT      | Total free size (unit: bytes)   |