This view displays information about the share pool and large pool on all nodes.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID   | NUMBER     | Group ID                        |
| GROUP_NODE_ID | NUMBER  | Node ID within the group       |
| INST_ID    | NUMBER     | Instance ID                     |
|   POOL | VARCHAR(32) | Memory pool type<br />* SHARE POOL <br />* LARGE POOL  |
| NAME | VARCHAR(32) |  Names of sub-pools within the Share Pool<br />* SQL POOL<br />* DICTIONARY CACHE POOL<br />* LOCK POOL<br />* CURSOR POOL<br />* DSTB POOL (Exists only in ISC Distributed Cluster Deployment)<br />* USER LOCK POOL<br />* RECOVERY BUDDY POOL<br />* BLOCK RES POOL  (Exists only in YAC or distributed clusters)<br />* NON BLOCK RES POOL  (Exists only in YAC or distributed clusters)<br />* GRC REQ POOL  (Exists only in YAC or distributed clusters)<br />* GCS PC POOL  (Exists only in YAC or distributed clusters)<br />* GRC OBJECT POOL  (Exists only in YAC or distributed clusters)<br />* GLS POOL  (Exists only in YAC or distributed clusters)<br />* STREAM POOL<br />* GCS LOCK POOL  (Exists only in YAC or distributed clusters)<br />* FREE POOL <br/><br/> Memory Information of the Large Pool: <br />* free memory: Memory currently in use <br />* free memory: Free memory     |
| BYTES | BIGINT | Capacity of the memory pool (in bytes)  |
