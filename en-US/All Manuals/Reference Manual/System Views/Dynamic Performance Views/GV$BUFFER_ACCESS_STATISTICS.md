This view shows statistics for session-level buffer access.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| SID | INTEGER | Session ID |
| ACCESS_BLOCKS | BIGINT | Total number of accesses to the latest version data blocks |
| CR_BLOCK_GETS | BIGINT | Number of accesses to consistency read data blocks |
| DISK_READS | BIGINT | Number of physical reads generated |
| LOCK_CONFLICTS | BIGINT | Number of block lock conflicts |
| RECYCLE_REQUESTS | BIGINT | Number of buffer eviction requests generated |
| BLOCKS_INSPECTED | BIGINT | Number of blocks inspected during buffer eviction |
| DIRTY_INSPECTED | BIGINT | Number of dirty blocks encountered during buffer eviction |
| PINNED_INSPECTED | BIGINT | Number of pinned blocks encountered during buffer eviction |
| HOT_INSPECTED | BIGINT | Number of hot blocks encountered during buffer eviction |