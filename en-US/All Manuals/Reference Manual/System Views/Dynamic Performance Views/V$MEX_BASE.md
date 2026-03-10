This view displays information about the memory base address pool in the MEX pool on the current node.

|Field |Type |Description |
|-----------------------|--------|----------------|
|  HOLD_SIZE            | BIGINT | Capacity of memory currently held (in bytes)          |
| USING_SIZE           | BIGINT | Capacity of memory has been allocated (in bytes)        |
| UNUSED_SIZE          | BIGINT | Capacity of free memory (in bytes)        |
| FILL_TIMES           | BIGINT | Total number of cache misses across all areas |
| MAX_CONTINUOUS_SIZE | BIGINT | Capacity of the largest contiguous block of memory (in bytes)  |
