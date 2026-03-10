This view displays information about the EDEN part of the memory pool for all nodes in MEX.

|Field |Type |Description |
|----------------------|--------|----------------------------------------------------------------------------------------------------|
| GROUP_ID             | NUMBER  | Group ID                                                                                                 |
| GROUP_NODE_ID        | NUMBER  | Node ID within the group                                                                                 |
| INST_ID              | NUMBER  | Instance ID                                                                                              |
| HOLD_SIZE           | BIGINT  | Held memory (in bytes), controlled by parameters MEX_POOL_SIZE, WORK_AREA_HEAP_SIZE, WORK_AREA_POOL_SIZE, MAX_SESSIONS, PQ_POOL_SIZE |
| USING_SIZE          | BIGINT  | Used memory (in bytes)                                                                                   |
| UNUSED_SIZE         | BIGINT  | Unused memory (in bytes)                                                                                 |
| EMAP_SIZE           | BIGINT  | Size of the metadata index (in bytes), fixed value, related to HOLD_SIZE                              |
| EXTENT_HOLD_SIZE   | BIGINT  | Memory held by metadata (in bytes)                                                                        |
| EXTENT_USING_SIZE  | BIGINT  | Memory used by metadata (in bytes)                                                                         |
| EXTENT_UNUSED_SIZE | BIGINT  | Unused memory for metadata (in bytes)                                                                     |