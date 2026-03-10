In the ISC Distributed Cluster Deployment section, this view displays the summary information of the internal network link status of all nodes.

In the Standalone/YAC/Distributed Cluster Deployment section, this view is empty.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID              | NUMBER     | Group ID                                                                       |
| GROUP_NODE_ID         | NUMBER     | Node ID within the group                                                       |
| INST_ID               | NUMBER     | Instance ID                                                                    |
| ENDPOINT              | SMALLINT   | Communication ID                                                               |
| PEER_GROUP_ID         | INTEGER    | Peer group ID                                                                  |
| PEER_GROUP_NODE_ID    | INTEGER    | Node ID within the peer group                                                  |
| PEER_ENDPOINT          | SMALLINT   | Peer communication ID                                                          |
| STATUS                | VARCHAR(16)| Connection status<br>\* INVALID: Invalid<br>\* VALID: Valid<br>\* PENDING: Pending |
| START_TIME            | TIMESTAMP  | Node start time                                                                |
| LAST_ACTIVE           | TIMESTAMP  | Last heartbeat activation time of the node                                     |
| PEER_ADDRESS          | VARCHAR(108)| Peer communication address                                                      |
| DISCONNECT_TIMES      | SMALLINT   | Number of disconnections                                                        |
| LINK_NUM              | SMALLINT   | Number of links from local to peer node                                         |
| SUM_LINK_NUM          | SMALLINT   | Cumulative number of links from local to peer node, including disconnected ones  |
| SEND_MSG_NUM          | BIGINT     | Total number of messages sent to the target node since the node was started     |
| SEND_MSG_SIZE         | BIGINT     | Total size of messages sent to the target node since the node was started (bytes) |
| SEND_MSG_AVG_SIZE     | BIGINT     | Average size of messages sent to the target node since the node was started (bytes) |
| RECV_MSG_NUM          | BIGINT     | Total number of messages received since the node was started                   |
| RECV_MSG_SIZE         | BIGINT     | Total size of messages received since the node was started (bytes)            |
| RECV_MSG_AVG_SIZE     | BIGINT     | Average size of received messages since the node was started (bytes)          |
| MAX_SEND_BLOCK_TIME   | INTEGER    | Longest single send time (milliseconds)                                        |
| MAX_RECV_BLOCK_TIME   | INTEGER    | Longest single receive processing time (milliseconds)                          |
| MAX_RECV_BLOCK_CMD    | SMALLINT   | Communication command during the longest single receive processing time        |
| THROUGHPUT            | BIGINT     | Node throughput (bytes/minute)                                                |
| CONTROL_SEND_TIMEOUT_CNT | INTEGER  | Cumulative number of control channel send timeouts from local to peer node    |
| CONTROL_RECV_TIMEOUT_CNT | INTEGER  | Cumulative number of control channel receive timeouts from local to peer node  |
| DATA_SEND_TIMEOUT_CNT   | INTEGER   | Cumulative number of data channel send timeouts from local to peer node        |
| DATA_RECV_TIMEOUT_CNT   | INTEGER   | Cumulative number of data channel receive timeouts from local to peer node     |