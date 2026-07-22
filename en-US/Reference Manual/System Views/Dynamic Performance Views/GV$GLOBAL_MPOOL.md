This view mainly describes the statistics of instance-level global application pool, SQL pool and dictionary cache.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| NAME | VARCHAR(64) | Name of the memory pool<br>* application pool: Global application pool<br>* sql main pool<br>* sql pl pool<br>* dictionary cache |
| TOTAL\_SIZE | BIGINT | Total size of the memory pool (unit: bytes) |
| BLOCK\_SIZE | INTEGER | Size of each block in the memory pool (unit: bytes) |
| TOTAL\_BLOCK\_COUNT | INTEGER | Total number of blocks contained in the memory pool |
| FREE\_BLOCK\_COUNT | INTEGER | Number of free blocks in the memory pool |
| USED\_SIZE | BIGINT | Size of the space used in the memory pool (unit: bytes) |
| FREE\_SIZE | BIGINT | Size of the space unused in the memory pool (unit: bytes) |
| BLOCK\_HANDLE\_CREATE\_TIMES | BIGINT | Number of times blocks in the memory pool have been created |
| BLOCK\_HANDLE\_DESTROY\_TIMES | BIGINT | Number of times blocks in the memory pool have been destroyed |
| BLOCK\_ALLOC\_TIMES | BIGINT | Number of times blocks in the memory pool have been allocated |
| POOL\_EXTEND\_TIMES | INTEGER | Number of times space in the memory pool has been extended |
| POOL\_RECYCLE\_TIMES | INTEGER | Number of times space in the memory pool has been recycled |