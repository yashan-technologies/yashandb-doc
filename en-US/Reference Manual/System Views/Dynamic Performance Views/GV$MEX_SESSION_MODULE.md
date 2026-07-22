This view shows the usage of various functionality modules in the MEX pool across all sessions of each node.

|Field |Type |Description |
|---------------------|-------------|---------------|
| GROUP_ID            | NUMBER      | Group ID                        |
| GROUP_NODE_ID       | NUMBER      | Node ID within the group         |
| INST_ID             | NUMBER      | Instance ID                     |
| SID                 | SMALLINT    | Session ID                      |
| TAG\_ID             | INTEGER     |  Memory component ID  <br/>* 1: Application pool  <br/>* 2: Global application pool  <br/>* 3: Parallel query pool  <br/>* 4: Metadata of the MEX pool  <br/>* 0: Memory available for SQL batch execution and JSON           |
| NAME                | VARCHAR(64) |  Memory component name <br/>* APP MEM INIT: Session-private heap memory  <br/>* APP MEM EXTEND: Global application pool  <br/>* PQ POOL: Parallel query pool  <br/>* POOL META: Metadata of the MEX pool  <br/>* OTHERS: Memory available for SQL batch execution and JSON             |
| USING\_SIZE         | BIGINT      | Memory in use (unit: bytes)    |
| TOTAL\_ALLOC\_TIMES | BIGINT      | Total allocation count           |
| TOTAL\_ALLOC\_SIZE  | BIGINT      | Total allocation size (unit: bytes) |
| TOTAL\_FREE\_TIMES  | BIGINT      | Total free count                 |
| TOTAL\_FREE\_SIZE   | BIGINT      | Total free size (unit: bytes)   |
