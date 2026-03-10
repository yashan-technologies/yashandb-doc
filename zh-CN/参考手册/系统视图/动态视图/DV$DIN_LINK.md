本视图显示分布式集群中所有节点内部每条链路的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ENDPOINT | SMALLINT | 通讯ID |
| ADDRESS | VARCHAR(64) | 本端地址 |
| PEER_ENDPOINT | SMALLINT | 对端通讯ID |
| PEER_ADDRESS | VARCHAR(64) | 对端通讯地址 |
| LINK_LEVEL | VARCHAR(16) | 链路级别<br>\* INNER：内部链路<br>\* CTRL：控制链路 <br>\* DATA：数据链路|
| LINK_ID | SMALLINT | 链路ID |
| CONN_VERSION | INTEGER | 连接版本 |
| PEER_NODE_VERSION | SMALLINT | 对端节点版本 |
| LOCAL_CONN_VERSION | TINYINT | 本地连接版本 |
| PEER_CONN_VERSION | TINYINT | 对端连接版本 |
| LINK_CREATE_TIME | TIMESTAMP | 连接创建时间 |
| STATUS | VARCHAR(16) | 连接状态<br>\* INVALID：无效 <br>\* INIT：初始化 <br>\* HANDSHAKE：握手 <br>\* STARTING：准备 <br>\* STARTED：就绪|
| SEND_NUM | BIGINT | 从链路创建到目前为止发送的消息总数 |
| SEND_SIZE | BIGINT | 从链路创建到目前为止发送的消息总大小（单位：字节） |
| RECV_NUM | BIGINT | 从链路创建到目前为止接收的消息总数 |
| RECV_SIZE | BIGINT | 从链路创建到目前为止接收的消息总大小（单位：字节） |
| LAST_SEND_TIME | TIMESTAMP | 链路最后发送时间 |
| LAST_RECV_TIME | TIMESTAMP | 链路最后接收时间 |
| SEND_QUEUE_SIZE | INTEGER | TCP发送缓冲区使用大小（单位：字节），默认最大2M |
| RECV_QUEUE_SIZE | INTEGER | TCP接收缓冲区使用大小（单位：字节），默认最大2M |
| RESPONSE_TIME | NUMBER | 链路响应速度（单位：毫秒） |
| THROUGHPUT | BIGINT | 链路吞吐量（单位：字节每分钟） |
