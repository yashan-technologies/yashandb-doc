This view displays the session connection information created by all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- |-----------------------------------------------------------------|
| GROUP_ID                  | INTEGER   | Group ID                                                        |
| GROUP_NODE_ID             | INTEGER   | Node ID within the group                                        |
| GLOBAL_SESSION_ID         | INTEGER   | Distributed global session ID                                    |
| HANDLER_ID                | SMALLINT  | Own handler ID                                                 |
| SERIAL#                   | INTEGER   | Assigned sequence number                                        |
| SEQ_ID | INTEGER | sequence ID                                                     |
| SQL_ID                    | VARCHAR(13)| SQL ID of the currently executing session (hash/encryption result of SQL text) |
| PEER_GROUP_ID             | INTEGER   | Peer group ID                                                  |
| PEER_GROUP_NODE_ID        | INTEGER   | Node ID within the peer group                                   |
| PEER_ENDPOINT              | SMALLINT  | Peer node communication ID                                      |
| STATUS                    | VARCHAR(8)| Connection status<br>* IDLE: Idle<br>* BUSY: Busy<br>* ERROR: Error<br>* FAILED: Failed |
| WAIT_ACK_TIME             | BIGINT    | Wait acknowledgment time                                        |
| IS_CONNECTED              | VARCHAR(8)| Whether CN is connected to the corresponding node<br>* YES: Yes<br>* NO: No |
| IS_FIRST                  | VARCHAR(8)| Whether it is the first connection<br>* YES: Yes<br>* NO: No  |
| CONNECTION_VERSION         | INTEGER   | Connection version using ICS                                    |
| PEER_HANDLER_ID           | INTEGER   | Peer handler ID                                               |
| PEER_HANDLER_SERIAL#      | INTEGER   | Peer handler sequence number                                   |