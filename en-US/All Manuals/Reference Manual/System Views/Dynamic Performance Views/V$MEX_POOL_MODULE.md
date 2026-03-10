This view displays information about each memory component in the MEX pool on the current node.

|Field |Type |Description |
|---------------------|-------------|---------------|
| TAG_ID             | INTEGER      |  Memory component ID  <br/>* 1: Application pool  <br/>* 2: Global application pool  <br/>* 3: Parallel query pool  <br/>* 4: Metadata of the MEX pool  <br/>* 0: Memory available for SQL batch execution and JSON                 |
| NAME                | VARCHAR(64)  |  Memory component name <br/>* APP MEM INIT: Session-private heap memory  <br/>* APP MEM EXTEND: Global application pool  <br/>* PQ POOL: Parallel query pool  <br/>* POOL META: Metadata of the MEX pool  <br/>* OTHERS: Memory available for SQL batch execution and JSON              |
| TOTAL_ALLOC_TIMES | BIGINT       | Historical allocation times   |
| TOTAL_ALLOC_SIZE  | BIGINT       | Historical allocation size (in bytes) |
| TOTAL_FREE_TIMES  | BIGINT       | Historical release times      |
| TOTAL_FREE_SIZE   | BIGINT       | Historical release size (in bytes) |
