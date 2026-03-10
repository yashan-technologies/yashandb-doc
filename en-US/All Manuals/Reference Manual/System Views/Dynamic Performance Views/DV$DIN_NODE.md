This view displays summary information about the internal network link states of all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ENDPOINT | SMALLINT | Communication ID |
| PEER_GROUP_ID | INTEGER | Peer Group ID |
| PEER_GROUP_NODE_ID | INTEGER | Node ID within the peer group |
| PEER_ENDPOINT | SMALLINT | Peer Communication ID |
| STATUS | VARCHAR(16) | Connection Status<br>\* INVALID: Invalid<br>\* VALID: Valid<br>\* PENDING: Pending |
| START_TIME | TIMESTAMP | Node Start Time |
| LAST_ACTIVE | TIMESTAMP | Last Heartbeat Activation Time of the Node |
| PEER_ADDRESS | VARCHAR(108) | Peer Communication Address |
| DISCONNECT_TIMES | SMALLINT | Number of Disconnections |
| LINK_NUM | SMALLINT | Number of Links from Local to Peer Node |
| SUM_LINK_NUM | SMALLINT | Cumulative Number of Links from Local to Peer Node, including those that have been disconnected |
| SEND_MSG_NUM | BIGINT | Total Number of Messages Sent to the Target Node since the Node was Started |
| SEND_MSG_SIZE | BIGINT | Total Size of Messages Sent to the Target Node since the Node was Started (Unit: Bytes) |
| SEND_MSG_AVG_SIZE | BIGINT | Average Size of Messages Sent to the Target Node since the Node was Started (Unit: Bytes) |
| RECV_MSG_NUM | BIGINT | Total Number of Messages Received since the Node was Started |
| RECV_MSG_SIZE | BIGINT | Total Size of Messages Received since the Node was Started (Unit: Bytes) |
| RECV_MSG_AVG_SIZE | BIGINT | Average Size of Messages Received since the Node was Started (Unit: Bytes) |
| MAX_SEND_BLOCK_TIME | INTEGER | Longest Time Taken for a Single Send (Unit: Milliseconds) |
| MAX_RECV_BLOCK_TIME | INTEGER | Longest Time Taken for a Single Receive (Unit: Milliseconds) |
| MAX_RECV_BLOCK_CMD | SMALLINT | Communication Command with the Longest Processing Time for a Single Receive |
| THROUGHPUT | BIGINT | Node Throughput (Unit: Bytes per Minute) |
| CONTROL_SEND_TIMEOUT_CNT | INTEGER | Cumulative Number of Control Channel Send Timeouts between Local and Peer Node |
| CONTROL_RECV_TIMEOUT_CNT | INTEGER | Cumulative Number of Control Channel Receive Timeouts between Local and Peer Node |
| DATA_SEND_TIMEOUT_CNT | INTEGER | Cumulative Number of Data Channel Send Timeouts between Local and Peer Node |
| DATA_RECV_TIMEOUT_CNT | INTEGER | Cumulative Number of Data Channel Receive Timeouts between Local and Peer Node |