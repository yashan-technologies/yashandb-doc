This view shows information about the EDEN portion of the current node's MEX memory pool.

|Field |Type |Description |
|----------------------|--------|----------------------------------------------------------------------------------------------------|
| HOLD_SIZE            | BIGINT  | Memory held (in bytes) controlled by parameters MEX_POOL_SIZE, WORK_AREA_HEAP_SIZE, WORK_AREA_POOL_SIZE, MAX_SESSIONS, PQ_POOL_SIZE |
| USING_SIZE           | BIGINT  | Memory in use (in bytes)                                                                                     |
| UNUSED_SIZE          | BIGINT  | Memory unused (in bytes)                                                                                     |
| EMAP_SIZE            | BIGINT  | Size of the metadata index (in bytes), fixed value related to HOLD_SIZE                                   |
| EXTENT_HOLD_SIZE    | BIGINT  | Memory held by the metadata (in bytes)                                                                        |
| EXTENT_USING_SIZE   | BIGINT  | Memory used by the metadata (in bytes)                                                                        |
| EXTENT_UNUSED_SIZE  | BIGINT  | Memory unused by the metadata (in bytes)                                                                      |