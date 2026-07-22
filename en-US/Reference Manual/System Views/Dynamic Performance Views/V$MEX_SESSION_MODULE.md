This view displays the usage of various functionality modules in the MEX pool across different sessions of the current node.

|Field |Type |Description |
|---------------------|-------------|---------------|
| SID                 | SMALLINT    | Session ID            |
| TAG\_ID             | INTEGER     |  Memory component ID  <br/>* 1: Application pool  <br/>* 2: Global application pool  <br/>* 3: Parallel query pool  <br/>* 4: Metadata of the MEX pool  <br/>* 0: Memory available for SQL batch execution and JSON           |
| NAME                | VARCHAR(64) |  Memory component name <br/>* APP MEM INIT: Session-private heap memory  <br/>* APP MEM EXTEND: Global application pool  <br/>* PQ POOL: Parallel query pool  <br/>* POOL META: Metadata of the MEX pool  <br/>* OTHERS: Memory available for SQL batch execution and JSON             |
| USING\_SIZE         | BIGINT      | Memory in use (unit: bytes) |
| TOTAL\_ALLOC\_TIMES | BIGINT      | Total allocation counts |
| TOTAL\_ALLOC\_SIZE  | BIGINT      | Total allocation size (unit: bytes) |
| TOTAL\_FREE\_TIMES  | BIGINT      | Total release counts   |
| TOTAL\_FREE\_SIZE   | BIGINT      | Total release size (unit: bytes) |
