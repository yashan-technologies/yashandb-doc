This view displays statistics for session-level buffer accesses.

|Field |Type |Description |
| --- | --- | --- |
| SID                | INTEGER | Session ID                                        |
| ACCESS_BLOCKS      | BIGINT  | Total number of latest version data block accesses |
| CR_BLOCK_GETS      | BIGINT  | Number of consistent read data block accesses     |
| DISK_READS         | BIGINT  | Number of physical reads generated                 |
| LOCK_CONFLICTS     | BIGINT  | Number of block lock conflicts                     |
| RECYCLE_REQUESTS   | BIGINT  | Number of buffer eviction requests generated       |
| BLOCKS_INSPECTED   | BIGINT  | Number of blocks inspected during buffer eviction  |
| DIRTY_INSPECTED    | BIGINT  | Number of dirty blocks encountered during buffer eviction |
| PINNED_INSPECTED   | BIGINT  | Number of pinned blocks encountered during buffer eviction |
| HOT_INSPECTED      | BIGINT  | Number of hot blocks encountered during buffer eviction |