This view shows information about the system shared memory pool.

|Field |Type |Description |
| --- | --- | --- |
| NAME | VARCHAR(32) | Memory pool name |
| SIZE | BIGINT | Size of the memory pool (unit: bytes) |
| MEMORY_INIT_TYPE | VARCHAR(16) | Method of initializing the memory pool<br>* PERCENTAGE: Initialized based on percentage<br>* NUMBER: Initialized based on fixed size |
| MEMORY_MANAGE_TYPE | VARCHAR(16) | Memory management method<br>* VARIABLE: Memory can be dynamically scaled<br>* FIXED: Fixed memory size |
| MEMORY_REQUEST_COUNT | BIGINT | Number of dynamic memory allocation requests in the memory pool |
| MEMORY_REQUEST_SIZE | BIGINT | Size of dynamic memory allocations in the memory pool (unit: bytes) |
| MEMORY_FREE_COUNT | BIGINT | Number of dynamic memory release requests in the memory pool |
| MEMORY_FREE_SIZE | BIGINT | Size of dynamic memory releases in the memory pool (unit: bytes) |