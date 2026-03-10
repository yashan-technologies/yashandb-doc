This view displays detailed information about the memory pool during the vectorization calculation process.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER        | Group ID                                     |
| GROUP_NODE_ID    | NUMBER        | Node ID within the group                     |
| INST_ID          | NUMBER        | Instance ID                                  |
| SIZE_CLASS       | VARCHAR(64)   | Size of a single memory block managed       |
| FREE_MEMORY      | BIGINT        | Amount of free memory                        |
| USED_MEMORY      | BIGINT        | Amount of memory currently in use            |
| MAX_USED_MEMORY  | BIGINT        | Peak value of used memory                    |
| ALLOC_TIMES      | BIGINT        | Total number of successful memory block allocations to the pool |
| DEALLOC_TIMES    | BIGINT        | Total number of memory blocks released from the pool |
| SYS_ALLOC_TIMES  | BIGINT        | Total number of successful memory block allocations to the operating system |
| SYS_DEALLOC_TIMES| BIGINT        | Total number of memory blocks released to the operating system |