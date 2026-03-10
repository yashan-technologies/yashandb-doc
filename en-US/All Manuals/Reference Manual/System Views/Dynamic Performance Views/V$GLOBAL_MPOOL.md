This view mainly describes the statistics of instance-level global application pool, SQL buffer pool and dictionary cache pool.

|Field |Type |Description |
| --- | --- | --- |
| NAME | VARCHAR(64) | Name of the memory pool<br>* application pool: Global application pool<br>* sql main pool<br>* sql pl pool<br>* dictionary cache |
| TOTAL_SIZE | BIGINT | Total size of the memory pool (unit: bytes) |
| BLOCK_SIZE | INTEGER | Size of each block in the memory pool (unit: bytes) |
| TOTAL_BLOCK_COUNT | INTEGER | Total number of blocks contained in the memory pool |
| FREE_BLOCK_COUNT | INTEGER | Number of free blocks in the memory pool |
| USED_SIZE | BIGINT | Size of the space used in the memory pool (unit: bytes) |
| FREE_SIZE | BIGINT | Size of the space unused in the memory pool (unit: bytes) |
| BLOCK_HANDLE_CREATE_TIMES | BIGINT | Number of times blocks in the memory pool have been created |
| BLOCK_HANDLE_DESTROY_TIMES | BIGINT | Number of times blocks in the memory pool have been destroyed |
| BLOCK_ALLOC_TIMES | BIGINT | Number of times blocks in the memory pool have been allocated |
| POOL_EXTEND_TIMES | INTEGER | Number of times the space in the memory pool has been extended |
| POOL_RECYCLE_TIMES | INTEGER | Number of times the space in the memory pool has been recycled |