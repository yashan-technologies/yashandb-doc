This view displays primary-standby replication summary information.

|Field |Type |Description |
| ------------ |---------------| ------------------------------------ |
| GROUP_ID      | NUMBER         | Group ID                            |
| GROUP_NODE_ID | NUMBER         | Node ID within the group           |
| INST_ID       | NUMBER         | Instance ID                         |
| MESSAGE_NUM   | INTEGER        | Message sequence number             |
| TYPE          | VARCHAR(32)    | Type of the message                 |
| SEVERITY      | VARCHAR(16)    | Severity of the message             |
| DEST_ID       | TINYINT        | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter |
| ERROR_CODE    | INTEGER        | Error code number                   |
| FIRST_TIME    | DATE           | Time of first occurrence            |
| LAST_TIME     | DATE           | Time of last occurrence             |
| REPEAT_TIME   | BIGINT         | Number of repetitions               |
| MESSAGE       | VARCHAR(8192)  | Message content                     |