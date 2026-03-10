This view displays information about all log files for Flashback in the entire database.

It is only applicable to Standalone Deployment; this view is meaningless in other deployment types.

|Field |Type |Description |
| --- | --- | --- |
| FILE_NAME   | VARCHAR(256) | File name                          |
| FILE_NUM    | INTEGER      | File ID                            |
| THREAD#     | TINYINT      | Instance ID to which the file belongs |
| SEQUENCE#   | INTEGER      | File sequence                     |
| FILE_SIZE   | BIGINT       | Size occupied by the file (in bytes) |
| FIRST_CHANGE# | BIGINT     | SCN corresponding to the file creation |
| FIRST_TIME  | TIMESTAMP    | Timestamp corresponding to the file creation |