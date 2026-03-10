This view displays the information of the AREA section of the current node's MEX memory pool.

|Field |Type |Description |
|----------------|---------|----------------|
| AREA_ID          | INTEGER   | Area ID                           |
| HOLD_SIZE        | BIGINT    | Held memory (unit: bytes)        |
| USING_SIZE       | BIGINT    | Used memory (unit: bytes)        |
| UNUSED_SIZE      | BIGINT    | Unused memory (unit: bytes)      |
| DECAY_TIMES      | BIGINT    | Number of times memory block sorting was triggered |
| FILL_TIMES       | BIGINT    | Number of fills (number of buffer misses) |
| NUM_4K_PAGE      | BIGINT    | Number of 4KB pages in Buffer    |
| NUM_8K_PAGE      | BIGINT    | Number of 8KB pages in Buffer    |
| NUM_16K_PAGE     | BIGINT    | Number of 16KB pages in Buffer   |
| NUM_32K_PAGE     | BIGINT    | Number of 32KB pages in Buffer   |
| NUM_64K_PAGE     | BIGINT    | Number of 64KB pages in Buffer   |
| NUM_1M_PAGE      | BIGINT    | Number of 1MB pages in Buffer    |
| NUM_2M_PAGE      | BIGINT    | Number of 2MB pages in Buffer    |