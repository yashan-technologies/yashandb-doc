This view displays information about the memory area manager in the MEX pool on the current node.

|Field |Type |Description |
|----------------|---------|----------------|
|  AREA_ID          | INTEGER   | Area ID                           |
| HOLD_SIZE        | BIGINT    | Memory capacity held by the current area (unit: bytes)        |
| USING_SIZE       | BIGINT    | Capacity of allocated memory on the current area (unit: bytes)        |
| UNUSED_SIZE      | BIGINT    | Capacity of free memory on the current area (unit: bytes)      |
| DECAY_TIMES      | BIGINT    | Number of times memory block consolidation has been triggered on the current area |
| FILL_TIMES       | BIGINT    | Number of cache misses on the current area |
| NUM_4K_PAGE      | BIGINT    | Number of 4KB pages in idle memory of the current area   |
| NUM_8K_PAGE      | BIGINT    | Number of 8KB pages in idle memory of the current area    |
| NUM_16K_PAGE     | BIGINT    | Number of 16KB pages in idle memory of the current area   |
| NUM_32K_PAGE     | BIGINT    | Number of 32KB pages in idle memory of the current area   |
| NUM_64K_PAGE     | BIGINT    | Number of 64KB pages in idle memory of the current area   |
| NUM_1M_PAGE      | BIGINT    | Number of 1MB pages in idle memory of the current area    |
| NUM_2M_PAGE      | BIGINT    | Number of 2MB pages in idle memory of the current area   |
