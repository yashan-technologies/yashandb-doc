This view displays the information of pending messages in YAC message pool.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER  | Group ID                       |
| GROUP_NODE_ID| NUMBER  | Node ID within the group      |
| INST_ID      | NUMBER  | Instance ID                    |
| SRC_INSTANCE  | INTEGER | Sender node ID of the message  |
| SRC_SID      | INTEGER | Sender session ID of the message|
| DST_INSTANCE  | INTEGER | Receiver node ID of the message |
| DST_SID      | INTEGER | Receiver session ID of the message|
| NAME         | VARCHAR(32) | Message name                |
| SERIAL_NO    | INTEGER | Message sequence number        |
| SIZE         | INTEGER | Message size                   |
| IN_PROCESS   | BOOLEAN | Whether it is being processed   |
| TASK_SID     | INTEGER | Background session ID handling the message|