This view displays the summary information of the internal network statistics of the current node under distributed conditions. The Standalone Deployment query returns empty.

|Field |Type |Description |
| --- | --- | --- |
| ENDPOINT             | SMALLINT | Communication ID                                         |
| NODES_NUM            | SMALLINT | Number of communication nodes                            |
| DISCONNECT_TIMES     | SMALLINT | Number of disconnections                                  |
| LINK_NUM             | SMALLINT | Number of links from local to peer nodes                |
| SUM_LINK_NUM         | SMALLINT | Cumulative number of links from local to peer nodes, including those that have been disconnected |
| THREAD_NUM           | SMALLINT | Number of threads related to communication               |
| SEND_MSG_NUM         | BIGINT   | Total number of messages sent to the target node since the node was started |
| SEND_MSG_SIZE        | BIGINT   | Total size of messages sent to the target node since the node was started (units: bytes) |
| SEND_MSG_AVG_SIZE    | BIGINT   | Average size of messages sent to the target node since the node was started (units: bytes) |
| RECV_MSG_NUM         | BIGINT   | Total number of messages received since the node was started |
| RECV_MSG_SIZE        | BIGINT   | Total size of messages received since the node was started (units: bytes) |
| RECV_MSG_AVG_SIZE    | BIGINT   | Average size of received messages since the node was started (units: bytes) |
| MAX_SEND_BLOCK_TIME  | INTEGER  | Longest time taken for a single send (units: milliseconds) |
| MAX_RECV_BLOCK_TIME  | INTEGER  | Longest time taken for a single receive process (units: milliseconds) |
| MAX_RECV_BLOCK_CMD   | SMALLINT | Communication command during the longest receive processing time |
| CONTROL_SEND_TIMEOUT_CNT  | INTEGER  | Total number of control channel send timeouts since the node was started |
| CONTROL_RECV_TIMEOUT_CNT  | INTEGER  | Total number of control channel receive timeouts since the node was started |
| DATA_SEND_TIMEOUT_CNT | INTEGER  | Total number of data channel send timeouts since the node was started |
| DATA_RECV_TIMEOUT_CNT | INTEGER  | Total number of data channel receive timeouts since the node was started |