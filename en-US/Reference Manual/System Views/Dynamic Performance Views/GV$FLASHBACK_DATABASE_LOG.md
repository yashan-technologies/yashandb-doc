This view displays information related to the database flashback functionality.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | NUMBER  | Group ID                                                                |
| GROUP_NODE_ID      | NUMBER  | Node ID within the group                                                |
| INST_ID            | NUMBER  | Instance ID                                                             |
| OLDEST_FLASHBACK_SCN | BIGINT | The SCN corresponding to the oldest point in time that the database flashback can reach |
| OLDEST_FLASHBACK_TIME | TIMESTAMP(6) | The timestamp corresponding to the oldest point in time that the database flashback can reach |
| RETENTION_TARGET    | BIGINT  | The maximum retention period of database flashback log files (unit: minutes). Only log files exceeding this duration are eligible for automatic cleanup. |
| TOTAL_FILE_SIZE     | BIGINT  | The total space occupied by all database flashback log files (unit: bytes) |
