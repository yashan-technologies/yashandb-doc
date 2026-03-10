This view displays a summary of the internal network link status of the current node in a distributed environment. The standalone query returns empty.

|Field |Type |Description |
| --- | --- | --- |
| ENDPOINT | SMALLINT | Communication ID |
| PEER_GROUP_ID | INTEGER | Peer group ID |
| PEER_GROUP_NODE_ID | INTEGER | Peer group node ID |
| PEER_ENDPOINT | SMALLINT | Peer communication ID |
| STATUS | VARCHAR(16) | Connection status<br>* INVALID: Invalid<br>* VALID: Valid<br>* PENDING: Pending |
| START_TIME | TIMESTAMP | Node startup time |
| LAST_ACTIVE | TIMESTAMP | Last heartbeat activation time of the node |
| PEER_ADDRESS | VARCHAR(108) | Peer communication address |
| DISCONNECT_TIMES | SMALLINT | Number of disconnections |
| LINK_NUM | SMALLINT | Number of links from local to peer node |
| SUM_LINK_NUM | SMALLINT | Cumulative number of links from local to peer node, including already disconnected ones |
| SEND_MSG_NUM | BIGINT | Total number of messages sent to the target node from the node start-up to the present |
| SEND_MSG_SIZE | BIGINT | Total size of messages sent to the target node from the node start-up to the present (unit: bytes) |
| SEND_MSG_AVG_SIZE | BIGINT | Average size of messages sent to the target node from the node start-up to the present (unit: bytes) |
| RECV_MSG_NUM | BIGINT | Total number of messages received from the node start-up to the present |
| RECV_MSG_SIZE | BIGINT | Total size of messages received from the node start-up to the present (unit: bytes) |
| RECV_MSG_AVG_SIZE | BIGINT | Average size of messages received from the node start-up to the present (unit: bytes) |
| MAX_SEND_BLOCK_TIME | INTEGER | Longest time taken for a single send (unit: milliseconds) |
| MAX_RECV_BLOCK_TIME | INTEGER | Longest time taken for a single receive processing (unit: milliseconds) |
| MAX_RECV_BLOCK_CMD | SMALLINT | Communication command during the longest receive processing time |
| THROUGHPUT | BIGINT | Node throughput (unit: bytes/minute) |
| CONTROL_SEND_TIMEOUT_CNT | INTEGER | Cumulative count of control channel send timeouts between local and peer node |
| CONTROL_RECV_TIMEOUT_CNT | INTEGER | Cumulative count of control channel receive timeouts between local and peer node |
| DATA_SEND_TIMEOUT_CNT | INTEGER | Cumulative count of data channel send timeouts between local and peer node |
| DATA_RECV_TIMEOUT_CNT | INTEGER | Cumulative count of data channel receive timeouts between local and peer node |