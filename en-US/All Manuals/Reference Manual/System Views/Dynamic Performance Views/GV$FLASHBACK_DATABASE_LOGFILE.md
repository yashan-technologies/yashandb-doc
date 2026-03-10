This view displays information about all the flashback log files in the entire database.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER      | Group ID                   |
| GROUP_NODE_ID| NUMBER      | Node ID within the group   |
| INST_ID      | NUMBER      | Instance ID                |
| FILE_NAME    | VARCHAR(256)| File Name                  |
| FILE_NUM     | INTEGER     | File ID                    |
| THREAD#      | TINYINT     | Instance ID to which the file belongs |
| SEQUENCE#    | INTEGER     | File sequence              |
| FILE_SIZE    | BIGINT      | Size of the file (unit: bytes) |
| FIRST_CHANGE#| BIGINT      | SCN corresponding to when the file was created |
| FIRST_TIME   | TIMESTAMP   | Timestamp corresponding to when the file was created |
