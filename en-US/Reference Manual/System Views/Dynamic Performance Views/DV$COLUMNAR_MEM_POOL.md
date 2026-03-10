This view displays detailed information about the memory pool during the vectorization calculation process across all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER     | Group ID                                         |
| GROUP_NODE_ID    | INTEGER     | Node ID within the group                         |
| SIZE_CLASS       | VARCHAR(64) | Size of the individual memory block managed      |
| FREE_MEMORY      | BIGINT      | Amount of free memory                            |
| USED_MEMORY      | BIGINT      | Amount of memory currently in use                |
| MAX_USED_MEMORY  | BIGINT      | Peak amount of memory used                       |
| ALLOC_TIMES      | BIGINT      | Total number of successful memory block allocations to the memory pool |
| DEALLOC_TIMES    | BIGINT      | Total number of memory block deallocations from the memory pool |
| SYS_ALLOC_TIMES  | BIGINT      | Total number of successful memory block allocations from the operating system |
| SYS_DEALLOC_TIMES| BIGINT      | Total number of memory block deallocations to the operating system |