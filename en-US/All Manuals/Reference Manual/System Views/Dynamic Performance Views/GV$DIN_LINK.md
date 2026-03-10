In ISC Distributed Cluster Deployment, this view shows the summary information of the internal links for all nodes.

In Standalone/YAC/Distributed Cluster Deployment, this view is empty.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER    | Group ID           |
| GROUP_NODE_ID   | NUMBER    | Node ID within group |
| INST_ID         | NUMBER    | Instance ID        |
| ENDPOINT        | SMALLINT  | Communication ID    |
| ADDRESS         | VARCHAR(64)| Local Address      |
| PEER_ENDPOINT    | SMALLINT  | Peer Communication ID |
| PEER_ADDRESS    | VARCHAR(64)| Peer Communication Address |
| LINK_LEVEL      | VARCHAR(16)| Link Level<br>* INNER: internal link<br>* CTRL: control link <br>* DATA: data link|
| LINK_ID         | SMALLINT  | Link ID            |
| CONN_VERSION    | INTEGER   | Connection Version  |
| PEER_NODE_VERSION| SMALLINT  | Peer Node Version   |
| LOCAL_CONN_VERSION| TINYINT  | Local Connection Version |
| PEER_CONN_VERSION| TINYINT   | Peer Connection Version |
| LINK_CREATE_TIME| TIMESTAMP(6) | Connection Creation Time |
| STATUS          | VARCHAR(16)| Connection Status<br>* INVALID: invalid <br>* INIT: initializing <br>* HANDSHAKE: handshake <br>* STARTING: preparing <br>* STARTED: ready|
| SEND_NUM        | BIGINT    | Total number of messages sent since the link was created |
| SEND_SIZE       | BIGINT    | Total size of messages sent (in bytes) since the link was created |
| RECV_NUM        | BIGINT    | Total number of messages received since the link was created |
| RECV_SIZE       | BIGINT    | Total size of messages received (in bytes) since the link was created |
| LAST_SEND_TIME  | TIMESTAMP(6) | Last send time of the link |
| LAST_RECV_TIME  | TIMESTAMP(6) | Last receive time of the link |
| SEND_QUEUE_SIZE | INTEGER   | TCP send cache usage size (in bytes), default maximum 2M |
| RECV_QUEUE_SIZE | INTEGER   | TCP receive cache usage size (in bytes), default maximum 2M |
| RESPONSE_TIME   | NUMBER    | Link response speed (in milliseconds) |
| THROUGHPUT      | BIGINT    | Link throughput (in bytes/minute) |