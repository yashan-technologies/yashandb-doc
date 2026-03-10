This view shows information about each functionality module in the current node's MEX memory pool.

|Field |Type |Description |
|---------------------|-------------|---------------|
| TAG_ID             | INTEGER      | Module label ID              |
| NAME                | VARCHAR(64)  | Module label name             |
| TOTAL_ALLOC_TIMES | BIGINT       | Historical allocation times   |
| TOTAL_ALLOC_SIZE  | BIGINT       | Historical allocation size (in bytes) |
| TOTAL_FREE_TIMES  | BIGINT       | Historical release times      |
| TOTAL_FREE_SIZE   | BIGINT       | Historical release size (in bytes) |