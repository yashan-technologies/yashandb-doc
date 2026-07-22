This view displays information about the system share pool on all nodes.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER   | Group ID                                        |
| GROUP_NODE_ID    | NUMBER   | Node ID within the group                        |
| INST_ID          | NUMBER   | Instance ID                                     |
|  NAME | VARCHAR(32) |  Memory pool name<br />* SQL POOL<br />* DICTIONARY CACHE POOL<br />* LOCK POOL<br />* CURSOR POOL<br />* DSTB POOL (Exists only in ISC Distributed Cluster Deployment)<br />* USER LOCK POOL<br />* RECOVERY BUDDY POOL<br />* BLOCK RES POOL  (Exists only in YAC or distributed clusters)<br />* NON BLOCK RES POOL  (Exists only in YAC or distributed clusters)<br />* GRC REQ POOL  (Exists only in YAC or distributed clusters)<br />* GCS PC POOL  (Exists only in YAC or distributed clusters)<br />* GRC OBJECT POOL  (Exists only in YAC or distributed clusters)<br />* GLS POOL  (Exists only in YAC or distributed clusters)<br />* STREAM POOL<br />* GCS LOCK POOL  (Exists only in YAC or distributed clusters)<br />* FREE POOL |
| SIZE | BIGINT | Size of the memory pool (unit: bytes) |
| MEMORY_INIT_TYPE | VARCHAR(16) | Method of initializing the memory pool<br>* PERCENTAGE: Initialized based on percentage<br>* NUMBER: Initialized based on fixed size |
| MEMORY_MANAGE_TYPE | VARCHAR(16) | Memory management method<br>* VARIABLE: Memory can be dynamically scaled<br>* FIXED: Fixed memory size |
| MEMORY_REQUEST_COUNT | BIGINT | Number of dynamic memory allocation requests in the memory pool |
| MEMORY_REQUEST_SZIE | BIGINT | Size of dynamic memory allocations in the memory pool (unit: bytes) |
| MEMORY_FREE_COUNT | BIGINT | Number of dynamic memory release requests in the memory pool |
| MEMORY_FREE_SIZE | BIGINT | Size of dynamic memory releases in the memory pool (unit: bytes)    |
