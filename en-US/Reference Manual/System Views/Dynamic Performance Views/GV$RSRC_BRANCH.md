This view displays resource usage information for each branch in a branch database.

|Field |Type |Description |
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | Group ID               |
| GROUP_NODE_ID | NUMBER       | Node ID in current group      |
| INST_ID       | NUMBER       | Instance ID                   |
| BRANCH_NAME                  | VARCHAR(68) | BRANCH name |
| CPU_WAIT_TIME             | BIGINT      | Cumulative amount of time that sessions waited for CPU because of resource management. This does not include waits due to latch or enqueue contention, I/O waits, and so on. When CPU resources are not being actively managed, this value is set to zero. |
| CPU_WAITS                 | BIGINT      | Cumulative number of times all sessions in the consumer group had to wait for CPU because of resource management. This does not include waits due to latch or enqueue contention, I/O waits, and so on. When CPU resources are not being actively managed, this value is set to zero. |
| CONSUMED_CPU_TIME         | BIGINT      | Cumulative amount of CPU time consumed by all sessions in the consumer group (in milliseconds) |
| SHARED_POOL_BYTES         | BIGINT      | The current usage of shared pool by this BRANCH in bytes |
| SPA_BYTES                 | BIGINT      | The current usage of PGA by this BRANCH in bytes |
| MEMORY_USED_BYTES         | BIGINT      | Current memory usage by this BRANCH in bytes |
| MEMORY_LIMIT_BYTES        | BIGINT      | Memory limit by this BRANCH in bytes |
| BRANCH_ID                 | BIGINT      | The ID of the branch to which the data pertains.|
