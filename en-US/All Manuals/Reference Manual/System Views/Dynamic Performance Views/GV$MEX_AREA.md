This view displays information about the AREA portion of the MEX memory pool for all nodes.

|Field |Type |Description |
|----------------|---------|----------------|
| GROUP_ID         | NUMBER   | Group ID                              |
| GROUP_NODE_ID    | NUMBER   | Node ID within the group              |
| INST_ID          | NUMBER   | Instance ID                           |
| AREA_ID         | INTEGER  | Area ID                               |
| HOLD_SIZE       | BIGINT   | Memory held (unit: bytes)            |
| USING_SIZE      | BIGINT   | Memory in use (unit: bytes)          |
| UNUSED_SIZE     | BIGINT   | Unused memory (unit: bytes)          |
| DECAY_TIMES     | BIGINT   | Number of times memory block tidy-up was triggered |
| FILL_TIMES      | BIGINT   | Number of fills (buffer miss count)   |
| NUM_4K_PAGE    | BIGINT   | Number of 4KB pages in the buffer     |
| NUM_8K_PAGE    | BIGINT   | Number of 8KB pages in the buffer     |
| NUM_16K_PAGE   | BIGINT   | Number of 16KB pages in the buffer    |
| NUM_32K_PAGE   | BIGINT   | Number of 32KB pages in the buffer    |
| NUM_64K_PAGE   | BIGINT   | Number of 64KB pages in the buffer    |
| NUM_1M_PAGE    | BIGINT   | Number of 1MB pages in the buffer     |
| NUM_2M_PAGE    | BIGINT   | Number of 2MB pages in the buffer     |