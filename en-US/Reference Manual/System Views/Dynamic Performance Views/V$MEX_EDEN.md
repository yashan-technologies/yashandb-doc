This view displays information about the Eden Space in the MEX pool on the current node.

|Field |Type |Description |
|----------------------|--------|----------------------------------------------------------------------------------------------------|
|  HOLD\_SIZE            | BIGINT  | Total capacity of the MEX pool (in bytes) |
| USING\_SIZE           | BIGINT  | Capacity of memory already allocated (in bytes) <br/> Always equal to the `HOLD_SIZE` in V$MEX_BASE view     |
| UNUSED\_SIZE          | BIGINT  | Capacity of free memory that is either unallocated or has been reclaimed (in bytes)              |
| EMAP\_SIZE            | BIGINT  | Memory capacity occupied by the page table of the MEX pool (in bytes)              |
| EXTENT\_HOLD\_SIZE    | BIGINT  | Total memory capacity available for storing MEX pool metadata (in bytes)        |
| EXTENT\_USING\_SIZE   | BIGINT  | Capacity of memory already used to store MEX pool metadata(in bytes)          |
| EXTENT\_UNUSED\_SIZE  | BIGINT  | Remaining memory capacity available for storing MEX pool metadata (in bytes)           |
