This view displays information about all log files for Flashback in the entire database.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| FILE_NAME   | VARCHAR(256) | File name                          |
| FILE_NUM    | INTEGER      | File ID                            |
| THREAD#     | TINYINT      | Instance ID to which the file belongs |
| SEQUENCE#   | INTEGER      | File sequence                     |
| FILE_SIZE   | BIGINT       | Size occupied by the file (in bytes) |
| FIRST_CHANGE# | BIGINT     | SCN corresponding to the file creation |
| FIRST_TIME  | TIMESTAMP(6)    | TIMESTAMP(6) corresponding to the file creation |
