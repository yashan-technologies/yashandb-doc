This view is used to detect the usage of the plan cache.

|Field |Type |Description |
| --- | --- | --- |
| SQL_POOL_ID          | INTEGER  | SQL pool ID                                                                               |
| SQL_PART_ID          | INTEGER  | SQL pool partition ID                                                                      |
| MPOOL_NAME           | VARCHAR(64) | Name of the memory pool <br>* sql main pool<br>* sql pl pool                               |
| FREE_COUNT           | INTEGER  | Number of free SQL buffer objects (reserved field)                                             |
| LRU_COUNT            | INTEGER  | Number of SQL buffer objects in LRU                                                              |
| LRU_COLD_COUNT       | INTEGER  | Number of cold state SQL buffer objects in LRU                                                  |
| LRU_ADJUST_TIMES     | BIGINT   | Number of LRU adjustments                                                                         |
| LRU_ADJUST_STEPS     | BIGINT   | Total steps of LRU adjustments                                                                    |
| LRU_RECYCLE_STEPS    | BIGINT   | Total steps of LRU recycling                                                                      |
| CREATE_TOTAL         | BIGINT   | Number of SQL buffer objects created                                                              |
| REUSE_TOTAL          | BIGINT   | Number of times SQL buffer objects were reused<br>The same SQL from the same user can attempt to reuse the buffer |
| INVALIDATE_TOTAL     | BIGINT   | Number of times SQL buffer objects were invalidated                                              |
| CLOSE_TOTAL          | BIGINT   | Number of times SQL buffer objects were closed                                                    |
| RELEASE_TOTAL        | BIGINT   | Number of times SQL buffer objects were released<br>SQL buffer objects are reclaimed by the last statement that closed them after invalidation |
| RECYCLE_TOTAL        | BIGINT   | Number of times SQL buffer objects were recycled                                                  |
| CLEAN_TOTAL          | BIGINT   | Cleanup of infrequently used SQL buffer objects triggered by the object dictionary<br>Only unused SQL buffer objects will be cleaned |
| CURR_ACTIVE          | INTEGER  | Currently used SQL buffer objects                                                                 |
| CURR_INACTIVE        | INTEGER  | Currently unused SQL buffer objects                                                               |
| CURR_INVALID         | INTEGER  | Currently invalidated SQL buffer objects that still have statements open                          |
| CURR_ACTIVE_BLOCKS   | INTEGER  | Total pages held by currently used SQL buffer objects                                             |
| CURR_INACTIVE_BLOCKS | INTEGER  | Total pages held by currently unused SQL buffer objects                                           |
| CURR_INVALID_BLOCKS  | INTEGER  | Total pages held by currently invalidated SQL buffer objects that still have statements open      |