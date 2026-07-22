This view displays information about pending messages in YAC message pool.

|Field |Type |Description |
| --- | --- | --- |
| SRC_INSTANCE | INTEGER  | ID of the sending node         |
| SRC_SID      | INTEGER  | ID of the sending session      |
| DST_INSTANCE | INTEGER  | ID of the receiving node       |
| DST_SID      | INTEGER  | ID of the receiving session     |
| NAME         | VARCHAR(32) | Message name               |
| SERIAL_NO    | INTEGER  | Message sequence number        |
| SIZE         | INTEGER  | Message size                   |
| IN_PROCESS   | BOOLEAN  | Whether it is being processed  |
| TASK_SID     | INTEGER  | Background session ID processing the message |