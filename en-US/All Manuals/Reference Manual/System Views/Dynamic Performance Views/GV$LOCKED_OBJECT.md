This view displays the information of all current object locks.

|Field |Type |Description |
| ---------- |------------| ------------------------------------------------------------ |
| GROUP_ID     | NUMBER     | Group ID                                                     |
| GROUP_NODE_ID| NUMBER     | Node ID within the group                                     |
| INST_ID      | NUMBER     | Instance ID                                                  |
| XEXT         | SMALLINT   | The transaction area number corresponding to the current transaction |
| XNODE        | SMALLINT   | The number of the current transaction within the corresponding transaction area |
| XSN          | INTEGER    | The version number of the current transaction                |
| OBJECT_ID    | BIGINT     | Table ID                                                     |
| SESSION_ID   | SMALLINT   | Session ID                                                   |
| LMODE        | VARCHAR(8) | Lock type held by the session<br/>*   TS: Shared table lock<br/>*   TX: Exclusive table lock<br/>*   NONE: Waiting for table lock |