This view displays summary information for each link inside all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ENDPOINT | SMALLINT | Communication ID |
| ADDRESS | VARCHAR(64) | Local address |
| PEER_ENDPOINT | SMALLINT | Peer communication ID |
| PEER_ADDRESS | VARCHAR(64) | Peer communication address |
| LINK_LEVEL | VARCHAR(16) | Link level<br>\* INNER: Internal link<br>\* CTRL: Control link <br>\* DATA: Data link|
| LINK_ID | SMALLINT | Link ID |
| CONN_VERSION | INTEGER | Connection version |
| PEER_NODE_VERSION | SMALLINT | Peer node version |
| LOCAL_CONN_VERSION | TINYINT | Local connection version |
| PEER_CONN_VERSION | TINYINT | Peer connection version |
| LINK_CREATE_TIME | TIMESTAMP | Connection creation time |
| STATUS | VARCHAR(16) | Connection status<br>\* INVALID: Invalid <br>\* INIT: Initialization <br>\* HANDSHAKE: Handshake <br>\* STARTING: Preparing <br>\* STARTED: Ready|
| SEND_NUM | BIGINT | Total number of messages sent since the link was created |
| SEND_SIZE | BIGINT | Total size of messages sent since the link was created (unit: bytes) |
| RECV_NUM | BIGINT | Total number of messages received since the link was created |
| RECV_SIZE | BIGINT | Total size of messages received since the link was created (unit: bytes) |
| LAST_SEND_TIME | TIMESTAMP | Last send time of the link |
| LAST_RECV_TIME | TIMESTAMP | Last receive time of the link |
| SEND_QUEUE_SIZE | INTEGER | TCP send cache usage size (unit: bytes), default maximum 2M |
| RECV_QUEUE_SIZE | INTEGER | TCP receive cache usage size (unit: bytes), default maximum 2M |
| RESPONSE_TIME | NUMBER | Link response time (unit: milliseconds) |
| THROUGHPUT | BIGINT | Link throughput (unit: bytes per minute) |