This view is used to detect the usage of plan caches for all instances in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER   | Group ID                                                                                   |
| GROUP_NODE_ID       | NUMBER   | Node ID within the group                                                                   |
| INST_ID             | NUMBER   | Instance ID                                                                               |
| SQL_POOL_ID         | INTEGER  | SQL pool ID                                                                         |
| SQL_PART_ID         | INTEGER  | SQL pool partition ID                                                                |
| MPOOL_NAME           | VARCHAR(64) | Name of the memory pool <br>* sql main pool<br>* sql pl pool                               |
| FREE_COUNT          | INTEGER  | Number of free SQL buffer objects (reserved field)                                        |
| LRU_COUNT           | INTEGER  | Number of SQL buffer objects in LRU                                                       |
| LRU_COLD_COUNT      | INTEGER  | Number of cold state SQL buffer objects in LRU                                            |
| LRU_ADJUST_TIMES    | BIGINT   | Number of LRU adjustments                                                                   |
| LRU_ADJUST_STEPS    | BIGINT   | Total number of steps for LRU adjustments                                                  |
| LRU_RECYCLE_STEPS   | BIGINT   | Total number of steps for LRU recycling                                                   |
| CREATE_TOTAL        | BIGINT   | Number of SQL buffer object creations                                                        |
| REUSE_TOTAL         | BIGINT   | Number of SQL buffer object reuses<br>Same SQL from the same user can attempt to reuse buffers |
| INVALIDATE_TOTAL    | BIGINT   | Number of SQL buffer object invalidations                                                  |
| CLOSE_TOTAL         | BIGINT   | Number of SQL buffer object closures                                                        |
| RELEASE_TOTAL       | BIGINT   | Number of SQL buffer object releases<br>The SQL buffer objects are reclaimed after invalidation by the last statement that closed them |
| RECYCLE_TOTAL       | BIGINT   | Number of SQL buffer object recycling                                                       |
| CLEAN_TOTAL         | BIGINT   | Cleanup of infrequently used SQL buffer objects triggered by object dictionary<br>Only cleans SQL buffer objects that are not in use |
| CURR_ACTIVE         | INTEGER  | Number of currently used SQL buffer objects                                                |
| CURR_INACTIVE       | INTEGER  | Number of currently unused SQL buffer objects                                              |
| CURR_INVALID        | INTEGER  | Number of currently invalid SQL buffer objects that still have statements not closed       |
| CURR_ACTIVE_BLOCKS  | INTEGER  | Total pages held by currently used SQL buffer objects                                      |
| CURR_INACTIVE_BLOCKS| INTEGER  | Total pages held by currently unused SQL buffer objects                                    |
| CURR_INVALID_BLOCKS | INTEGER  | Total pages held by currently invalid SQL buffer objects that still have statements not closed |