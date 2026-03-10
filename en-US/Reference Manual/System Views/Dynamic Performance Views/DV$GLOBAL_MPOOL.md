This view displays the memory pool information at the instance level for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| NAME | VARCHAR(64) | Name of the memory pool<br/>* application pool: Public heap memory pool<br/>* sql main pool: SQL buffer pool<br/>* sql pl pool: Stored procedure cache pool<br/>* dictionary cache: Data dictionary buffer pool |
| TOTAL_SIZE | BIGINT | Total size of the memory pool (unit: bytes) |
| BLOCK_SIZE | INTEGER | Size of each block in the memory pool (unit: bytes) |
| TOTAL_BLOCK_COUNT | INTEGER | Total number of blocks in the memory pool |
| FREE_BLOCK_COUNT | INTEGER | Number of free blocks in the memory pool |
| USED_SIZE | BIGINT | Size of the space used in the memory pool (unit: bytes) |
| FREE_SIZE | BIGINT | Size of the unused space in the memory pool (unit: bytes) |
| BLOCK_HANDLE_CREATE_TIMES | BIGINT | Number of times blocks were created in the memory pool |
| BLOCK_HANDLE_DESTROY_TIMES | BIGINT | Number of times blocks were destroyed in the memory pool |
| BLOCK_ALLOC_TIMES | BIGINT | Number of times blocks were allocated in the memory pool |
| POOL_EXTEND_TIMES | INTEGER | Number of times the space in the memory pool was extended |
| POOL_RECYCLE_TIMES | INTEGER | Number of times the space in the memory pool was recycled |