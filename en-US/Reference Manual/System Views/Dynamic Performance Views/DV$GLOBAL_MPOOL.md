This view displays the memory pool information at the instance level for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| NAME | VARCHAR(64) | Name of the memory pool<br/>* application pool: Public heap memory pool<br/>* sql main pool: SQL pool<br/>* sql pl pool: Stored procedure cache pool<br/>* dictionary cache: Dictionary cache |
| TOTAL\_SIZE | BIGINT | Total size of the memory pool (unit: bytes) |
| BLOCK\_SIZE | INTEGER | Size of each block in the memory pool (unit: bytes) |
| TOTAL\_BLOCK\_COUNT | INTEGER | Total number of blocks in the memory pool |
| FREE\_BLOCK\_COUNT | INTEGER | Number of free blocks in the memory pool |
| USED\_SIZE | BIGINT | Size of the space used in the memory pool (unit: bytes) |
| FREE\_SIZE | BIGINT | Size of the unused space in the memory pool (unit: bytes) |
| BLOCK\_HANDLE\_CREATE\_TIMES | BIGINT | Number of times blocks were created in the memory pool |
| BLOCK\_HANDLE\_DESTROY\_TIMES | BIGINT | Number of times blocks were destroyed in the memory pool |
| BLOCK\_ALLOC\_TIMES | BIGINT | Number of times blocks were allocated in the memory pool |
| POOL\_EXTEND\_TIMES | INTEGER | Number of times the space in the memory pool was extended |
| POOL\_RECYCLE\_TIMES | INTEGER | Number of times the space in the memory pool was recycled |