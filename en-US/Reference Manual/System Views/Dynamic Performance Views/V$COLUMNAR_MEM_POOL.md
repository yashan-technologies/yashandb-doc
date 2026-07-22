This view displays detailed information about the memory pool during the vectorization calculation process.

|Field |Type |Description |
| --- | --- | --- |
| SIZE_CLASS    | VARCHAR(64) | The size of a single memory block being managed  |
| FREE_MEMORY   | BIGINT     | The amount of free memory                         |
| USED_MEMORY    | BIGINT     | The amount of memory currently in use            |
| MAX_USED_MEMORY | BIGINT   | The peak amount of memory used                   |
| ALLOC_TIMES   | BIGINT     | The total number of successful memory block allocations from the memory pool |
| DEALLOC_TIMES | BIGINT     | The total number of memory block deallocations back to the memory pool |
| SYS_ALLOC_TIMES | BIGINT   | The total number of successful memory block allocations from the operating system |
| SYS_DEALLOC_TIMES | BIGINT | The total number of memory block deallocations back to the operating system |