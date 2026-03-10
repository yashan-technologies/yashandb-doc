This view displays the YAC/Distributed Cluster global cache lock status.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER    | Group ID                                                                                                         |
| GROUP_NODE_ID    | NUMBER    | Node ID within the group                                                                                         |
| INST_ID          | NUMBER    | Instance ID                                                                                                      |
| TS# | INTEGER | Tablespace |
| FILE# | INTEGER | File number |
| BLK# | INTEGER | Data block number |
| OBJ | BIGINT | Object ID |
| RES_MODE | VARCHAR(9) | Resource mode<br>* FREE: Current data block cache is in idle state<br>* SHARE: Current data block cache is registered as shared read-only state in GCS<br>* EXCLUSIVE: Current data block cache is registered as exclusive writable state in GCS |
| MASTER | TINYINT | Instance where resource metadata information is located in cache, NULL when cache is invalid |
| GRC_VERSION | SMALLINT | Grc version corresponding to the instance where resource metadata information is located in cache, NULL when cache is invalid |