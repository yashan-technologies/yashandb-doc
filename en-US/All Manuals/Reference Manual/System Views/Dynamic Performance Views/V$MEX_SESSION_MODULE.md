This view displays the usage of various functionality modules in the MEX memory pool across different sessions of the current node.

|Field |Type |Description |
|---------------------|-------------|---------------|
| SID                 | SMALLINT    | Session ID            |
| TAG_ID             | INTEGER     | Module Tag ID         |
| NAME                | VARCHAR(64) | Module Tag Name       |
| USING_SIZE         | BIGINT      | Memory in use (unit: bytes) |
| TOTAL_ALLOC_TIMES | BIGINT      | Total allocation counts |
| TOTAL_ALLOC_SIZE  | BIGINT      | Total allocation size (unit: bytes) |
| TOTAL_FREE_TIMES  | BIGINT      | Total release counts   |
| TOTAL_FREE_SIZE   | BIGINT      | Total release size (unit: bytes) |