This view displays summary information of internal network statistics for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER  | Group ID                          |
| GROUP_NODE_ID        | INTEGER  | Node ID within the group         |
| ENDPOINT             | SMALLINT | Communication ID                  |
| NODES_NUM            | SMALLINT | Number of communication nodes      |
| DISCONNECT_TIMES     | SMALLINT | Number of disconnections           |
| LINK_NUM             | SMALLINT | Number of links from local to remote node |
| SUM_LINK_NUM         | SMALLINT | Total number of links from local to remote node, including those that have been disconnected |
| THREAD_NUM           | SMALLINT | Number of communication-related threads |
| SEND_MSG_NUM         | BIGINT   | Total number of messages sent to target node since the node was activated |
| SEND_MSG_SIZE        | BIGINT   | Total size of messages sent to target node since the node was activated (unit: bytes) |
| SEND_MSG_AVG_SIZE    | BIGINT   | Average size of messages sent to target node since the node was activated (unit: bytes) |
| RECV_MSG_NUM         | BIGINT   | Total number of messages received since the node was activated |
| RECV_MSG_SIZE        | BIGINT   | Total size of messages received since the node was activated (unit: bytes) |
| RECV_MSG_AVG_SIZE    | BIGINT   | Average size of messages received since the node was activated (unit: bytes) |
| MAX_SEND_BLOCK_TIME   | INTEGER  | Maximum duration for a single send operation (unit: milliseconds) |
| MAX_RECV_BLOCK_TIME   | INTEGER  | Maximum duration for a single receive operation (unit: milliseconds) |
| MAX_RECV_BLOCK_CMD    | SMALLINT | Communication command during the longest receive block time |
| CONTROL_SEND_TIMEOUT_CNT   | INTEGER  | Cumulative count of control channel send timeouts since the node was activated |
| CONTROL_RECV_TIMEOUT_CNT   | INTEGER  | Cumulative count of control channel receive timeouts since the node was activated |
| DATA_SEND_TIMEOUT_CNT   | INTEGER  | Cumulative count of data channel send timeouts since the node was activated |
| DATA_RECV_TIMEOUT_CNT   | INTEGER  | Cumulative count of data channel receive timeouts since the node was activated |