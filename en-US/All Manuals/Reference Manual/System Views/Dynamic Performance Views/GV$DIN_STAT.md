In the ISC Distributed Cluster Deployment section, this view displays the summary information of the internal network statistics for all nodes.

In the Standalone/YAC/Distributed Cluster Deployment section, this view is empty.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER    | Group ID                                               |
| GROUP_NODE_ID       | NUMBER    | Node ID within the group                               |
| INST_ID             | NUMBER    | Instance ID                                           |
| ENDPOINT            | SMALLINT  | Communication ID                                      |
| NODES_NUM           | SMALLINT  | Number of communication nodes                          |
| DISCONNECT_TIMES    | SMALLINT  | Number of disconnections                                |
| LINK_NUM            | SMALLINT  | Number of links from local to peer nodes              |
| SUM_LINK_NUM        | SMALLINT  | Total number of links from local to peer nodes, including those that are disconnected |
| THREAD_NUM          | SMALLINT  | Number of communication-related threads                |
| SEND_MSG_NUM        | BIGINT    | Total number of messages sent to the target node since the node was started |
| SEND_MSG_SIZE       | BIGINT    | Total size of messages sent to the target node since the node was started (unit: bytes) |
| SEND_MSG_AVG_SIZE   | BIGINT    | Average size of messages sent to the target node since the node was started (unit: bytes) |
| RECV_MSG_NUM        | BIGINT    | Total number of messages received since the node was started |
| RECV_MSG_SIZE       | BIGINT    | Total size of messages received since the node was started (unit: bytes) |
| RECV_MSG_AVG_SIZE   | BIGINT    | Average size of messages received since the node was started (unit: bytes) |
| MAX_SEND_BLOCK_TIME | INTEGER   | Maximum time taken for a single send operation (unit: milliseconds) |
| MAX_RECV_BLOCK_TIME | INTEGER   | Maximum time taken for a single receive operation (unit: milliseconds) |
| MAX_RECV_BLOCK_CMD  | SMALLINT  | Communication command during the maximum receive processing time |
| CONTROL_SEND_TIMEOUT_CNT | INTEGER | Total number of control channel send timeouts since the node was started |
| CONTROL_RECV_TIMEOUT_CNT | INTEGER | Total number of control channel receive timeouts since the node was started |
| DATA_SEND_TIMEOUT_CNT | INTEGER | Total number of data channel send timeouts since the node was started |
| DATA_RECV_TIMEOUT_CNT | INTEGER | Total number of data channel receive timeouts since the node was started |