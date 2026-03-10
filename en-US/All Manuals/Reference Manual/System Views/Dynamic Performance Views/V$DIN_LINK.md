This view shows the summary information of each link inside all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| ENDPOINT | SMALLINT | Communication ID |
| ADDRESS | VARCHAR(108) | Local Address |
| PEER_ENDPOINT | SMALLINT | Peer Communication ID |
| PEER_ADDRESS | VARCHAR(108) | Peer Communication Address |
| LINK_LEVEL | VARCHAR(16) | Link Level<br>\* INNER: Internal Link<br>\* CTRL: Control Link <br>\* DATA: Data Link|
| LINK_ID | SMALLINT | Link ID |
| CONN_VERSION | INTEGER | Connection Version |
| PEER_NODE_VERSION | SMALLINT | Peer Node Version |
| LOCAL_CONN_VERSION | TINYINT | Local Connection Version |
| PEER_CONN_VERSION | TINYINT | Peer Connection Version |
| LINK_CREATE_TIME | TIMESTAMP | Connection Creation Time |
| STATUS | VARCHAR(16) | Connection Status<br>\* INVALID: Invalid <br>\* INIT: Initializing <br>\* HANDSHAKE: Handshake <br>\* STARTING: Preparing <br>\* STARTED: Ready|
| SEND_NUM | BIGINT | Total number of messages sent since the link was created |
| SEND_SIZE | BIGINT | Total size of messages sent since the link was created (unit: bytes) |
| RECV_NUM | BIGINT | Total number of messages received since the link was created |
| RECV_SIZE | BIGINT | Total size of messages received since the link was created (unit: bytes) |
| LAST_SEND_TIME | TIMESTAMP | Last send time of the link |
| LAST_RECV_TIME | TIMESTAMP | Last receive time of the link |
| SEND_QUEUE_SIZE | INTEGER | TCP send cache usage size (unit: bytes), default maximum 2M |
| RECV_QUEUE_SIZE | INTEGER | TCP receive cache usage size (unit: bytes), default maximum 2M |
| RESPONSE_TIME | NUMBER | Link response speed (unit: milliseconds) |
| THROUGHPUT | BIGINT | Link throughput (unit: bytes/minute) |