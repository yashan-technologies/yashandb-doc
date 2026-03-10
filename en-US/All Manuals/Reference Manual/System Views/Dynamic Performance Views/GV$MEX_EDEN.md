This view displays information about the Eden Space in the MEX pool on all nodes.

|Field |Type |Description |
|----------------------|--------|----------------------------------------------------------------------------------------------------|
| GROUP_ID             | NUMBER  | Group ID                                                                                                 |
| GROUP_NODE_ID        | NUMBER  | Node ID within the group                                                                                 |
| INST_ID              | NUMBER  | Instance ID                                                                                              |
|  HOLD_SIZE            | BIGINT  | Total capacity of the MEX pool (in bytes) |
| USING_SIZE           | BIGINT  | Capacity of memory already allocated (in bytes) <br/> Always equal to the `HOLD_SIZE` in V$MEX_BASE view     |
| UNUSED_SIZE          | BIGINT  | Capacity of free memory that is either unallocated or has been reclaimed (in bytes)              |
| EMAP_SIZE            | BIGINT  | Memory capacity occupied by the page table of the MEX pool (in bytes)              |
| EXTENT_HOLD_SIZE    | BIGINT  | Total memory capacity available for storing MEX pool metadata (in bytes)        |
| EXTENT_USING_SIZE   | BIGINT  | Capacity of memory already used to store MEX pool metadata(in bytes)          |
| EXTENT_UNUSED_SIZE  | BIGINT  | Remaining memory capacity available for storing MEX pool metadata (in bytes)       |
