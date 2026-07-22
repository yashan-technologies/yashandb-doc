This view displays resource usage information for each container in a CDB.

|Field |Type |Description |
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | Group ID               |
| GROUP_NODE_ID | NUMBER       | Node ID in current group      |
| INST_ID       | NUMBER       | Instance ID                   |
|  PDB_NAME                  | VARCHAR(68) | PDB name |
| CPU_WAIT_TIME             | BIGINT      | Cumulative amount of time that sessions waited for CPU because of resource management. This does not include waits due to latch or enqueue contention, I/O waits, and so on. When CPU resources are not being actively managed, this value is set to zero. |
| CPU_WAITS                 | BIGINT      | Cumulative number of times all sessions in the consumer group had to wait for CPU because of resource management. This does not include waits due to latch or enqueue contention, I/O waits, and so on. When CPU resources are not being actively managed, this value is set to zero. |
| CONSUMED_CPU_TIME         | BIGINT      | Cumulative amount of CPU time consumed by all sessions in the consumer group (in milliseconds) |
| YIELDS                    | BIGINT      | Cumulative number of times that sessions in the consumer group had to yield CPU to other sessions because of quantum expiration. When CPU resources are not being actively managed, this value is set to zero.<br>The current value is fixed to NULL. |
| IO_SERVICE_TIME           | BIGINT      | Cumulative I/O wait time (in milliseconds).<br>The current value is fixed to NULL. |
| IO_SERVICE_WAITS          | BIGINT      | Total number of wait requests.<br>The current value is fixed to NULL.|
| SMALL_READ_MEGABYTES      | BIGINT      | Number of single block megabytes read.<br>The current value is fixed to NULL. |
| SMALL_WRITE_MEGABYTES     | BIGINT      | Number of single block megabytes written.<br>The current value is fixed to NULL. |
| LARGE_READ_MEGABYTES      | BIGINT      | Number of multiblock megabytes read.<br>The current value is fixed to NULL. |
| LARGE_WRITE_MEGABYTES     | BIGINT      | Number of multiblock megabytes written.<br>The current value is fixed to NULL. |
| SMALL_READ_REQUESTS       | BIGINT      | Number of single block read requests.<br>The current value is fixed to NULL. |
| SMALL_WRITE_REQUESTS      | BIGINT      | Number of single block write requests.<br>The current value is fixed to NULL. |
| LARGE_READ_REQUESTS       | BIGINT      | Number of multiblock read requests.<br>The current value is fixed to NULL. |
| LARGE_WRITE_REQUESTS      | BIGINT      | Number of multiblock write requests.<br>The current value is fixed to NULL. |
| PQS_COMPLETED             | BIGINT      | Compatibility only, and the current value is fixed to NULL.|
| PQ_SERVERS_USED           | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| PQS_QUEUED                | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| PQ_ACTIVE_TIME            | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| PQ_QUEUED_TIME            | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| PQ_QUEUE_TIME_OUTS        | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| CURRENT_PQS_ACTIVE        | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| CURRENT_PQ_SERVERS_ACTIVE | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| CURRENT_PQS_QUEUED        | BIGINT      | Compatibility only, and the current value is fixed to NULL. |
| SGA_BYTES                 | BIGINT      | The current SGA usage by this PDB in bytes.<br>The current value is fixed to NULL. |
| BUFFER_CACHE_BYTES        | BIGINT      | The current usage of buffer cache by this PDB in bytes.<br>The current value is fixed to NULL. |
| SHARED_POOL_BYTES         | BIGINT      | The current usage of shared pool by this PDB in bytes |
| SPA_BYTES                 | BIGINT      | The current usage of PGA by this PDB in bytes |
| MEMORY_USED_BYTES         | BIGINT      | Current memory usage by this PDB in bytes |
| MEMORY_LIMIT_BYTES        | BIGINT      | Memory limit by this PDB in bytes |
| CON_ID                    | BIGINT      | The ID of the container to which the data pertains. Possible values include:<br>- 0: This value is used for rows containing data that pertain to the entire CDB. This value is also used for rows in non-CDBs.<br>- 1: This value is used for rows containing data that pertain to only the root<br>- n: Where n is the applicable container ID for the rows containing data|
