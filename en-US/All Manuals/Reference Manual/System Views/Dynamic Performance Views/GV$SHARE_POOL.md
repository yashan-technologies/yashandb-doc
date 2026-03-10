This view displays information about the system shared memory pool.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER   | Group ID                                        |
| GROUP_NODE_ID    | NUMBER   | Node ID within the group                        |
| INST_ID          | NUMBER   | Instance ID                                     |
| NAME             | VARCHAR(32) | Memory pool name                               |
| SIZE             | BIGINT   | Memory pool size (unit: bytes)                |
| MEMORY_INIT_TYPE | VARCHAR(16) | Method of initializing the memory pool<br>* PERCENTAGE: Initialize by percentage <br>* NUMBER: Initialize by fixed size |
| MEMORY_MANAGE_TYPE | VARCHAR(16) | Memory management method<br>* VARIABLE: Memory can dynamically scale <br>* FIXED: Fixed memory size |
| MEMORY_REQUEST_COUNT | BIGINT | Number of dynamic memory requests for the pool  |
| MEMORY_REQUEST_SIZE  | BIGINT | Size of dynamic memory requests for the pool (unit: bytes) |
| MEMORY_FREE_COUNT    | BIGINT | Number of dynamic memory releases for the pool  |
| MEMORY_FREE_SIZE     | BIGINT | Size of dynamic memory releases for the pool (unit: bytes) |