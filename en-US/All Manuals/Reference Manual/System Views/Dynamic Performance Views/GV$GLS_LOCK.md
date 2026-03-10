This view displays the global lock status in YAC.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER    | Group ID                                                                                                         |
| GROUP_NODE_ID    | NUMBER    | Node ID within the group                                                                                         |
| INST_ID          | NUMBER    | Instance ID                                                                                                      |
| ID               | BIGINT    | Global lock ID                                                                                                   |
| TYPE             | VARCHAR(32) | Global lock TYPE<br>\*   OBJECT_LOCK: Object lock<br>\*   SEGMENT_LOCK: Segment lock<br>\*   SEGMENT_EXTEND_LOCK: Extended segment lock<br>\*   INTERVAL_EXTEND_LOCK: Interval partition extended lock<br>\*   USER_LOCK: User lock<br>\*   SYSTEM_LOCK: System lock<br>\*   SPC_EXTENT_LOCK: Tablespace extent lock<br>\*   ROLE_LOCK: Role lock<br>\*   UNKNOWN: Unknown lock type |
| RESOURCE_NAME    | VARCHAR(128) | Resource name                                                                                                   |
| GLOBAL_STATUS     | TINYINT   | Lock status of the MASTER RESOURCE of the Buffer, the MODE will not be cleared after the local release of the lock<br>\*   0: NONE, not registered<br>\*   1: SHARE, registered as a shared lock status<br>\*   2: EXCLUSIVE, registered as an exclusive lock status |
| LOCAL_STATUS      | TINYINT   | Local lock status<br>\*   0: IDLE, currently not using this lock<br>\*   1: SHARE, currently using the lock as a shared lock<br>\*   2: INTENTIONAL EXCLUSIVE, intentional exclusive lock<br>\*   3: EXCLUSIVE, currently using the lock as an exclusive lock |
| SHARE_COUNT      | SMALLINT  | Number of holders of the shared lock                                                                              |
| XID              | BIGINT    | XRM XID holding the exclusive lock, this field is used to display the transaction corresponding to the X lock; this field is only meaningful when LOCAL_STATUS=EXCLUSIVE, otherwise it is NULL |
| MASTER | TINYINT | Instance where resource metadata information is located in cache, NULL when cache is invalid |
| GRC_VERSION | SMALLINT | Grc version corresponding to the instance where resource metadata information is located in cache, NULL when cache is invalid |