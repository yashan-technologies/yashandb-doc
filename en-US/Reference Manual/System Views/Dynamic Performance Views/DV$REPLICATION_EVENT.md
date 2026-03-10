This view shows the primary-standby replication summary information for all nodes in the distributed cluster.

|Field |Type |Description |
| --- |---------------| --- |
| GROUP_ID      | INTEGER       | Group ID                         |
| GROUP_NODE_ID | INTEGER       | Node ID within the group        |
| MESSAGE_NUM   | INTEGER       | Message sequence number          |
| TYPE          | VARCHAR(32)   | Type of message                 |
| SEVERITY      | VARCHAR(16)   | Severity of message              |
| DEST_ID       | TINYINT       | Standby database ID, corresponds to ARCHIVE_DEST_x parameter |
| ERROR_CODE    | INTEGER       | Error code number                |
| FIRST_TIME    | DATE          | First occurrence time            |
| LAST_TIME     | DATE          | Last occurrence time             |
| REPEAT_TIME   | BIGINT        | Number of repetitions            |
| MESSAGE       | VARCHAR(8192) | Message content                  |