This view displays information about all the flashback restore points in the entire database.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER   | Group ID                                |
| GROUP_NODE_ID | NUMBER   | Node ID within the group                |
| INST_ID       | NUMBER   | Instance ID                             |
| NAME          | VARCHAR(68) | Restore point name                     |
| SCN           | BIGINT   | SCN represented by the restore point    |
| IS_GUARANTEE  | TINYINT  | Whether it is a permanent restore point<br/>* 0: No<br/>* 1: Yes |
| CREATE_TIME   | TIMESTAMP(6)| The TIMESTAMP corresponding to when the restore point was created |
