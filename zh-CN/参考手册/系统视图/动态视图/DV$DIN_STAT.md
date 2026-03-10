本视图显示分布式集群中所有节点的内部网络统计的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ENDPOINT | SMALLINT | 通信ID |
| NODES_NUM | SMALLINT | 通信节点数量 |
| DISCONNECT_TIMES | SMALLINT | 断连次数 |
| LINK_NUM | SMALLINT | 本地到对端节点的链路数量 |
| SUM_LINK_NUM | SMALLINT | 本地到对端节点的累计链路数量，包括已经断连的 |
| THREAD_NUM | SMALLINT | 通信相关线程数量 |
| SEND_MSG_NUM | BIGINT | 从节点拉起到目前为止发送给目标节点的消息总数 |
| SEND_MSG_SIZE | BIGINT | 从节点拉起到目前为止发送给目标节点的消息总大小（单位：字节） |
| SEND_MSG_AVG_SIZE | BIGINT | 从节点拉起到目前为止发送给目标节点的消息平均大小（单位：字节） |
| RECV_MSG_NUM | BIGINT | 从节点拉起到目前为止接收到的消息总数 |
| RECV_MSG_SIZE | BIGINT | 从节点拉起到目前为止接收到的消息总大小（单位：字节） |
| RECV_MSG_AVG_SIZE | BIGINT | 从节点拉起到目前为止接收到消息的平均大小（单位：字节） |
| MAX_SEND_BLOCK_TIME | INTEGER | 单次发送最长耗时（单位：毫秒） |
| MAX_RECV_BLOCK_TIME | INTEGER | 单次接收处理最长耗时（单位：毫秒） |
| MAX_RECV_BLOCK_CMD | SMALLINT | 单次接收处理最长耗时时的通讯命令 |
| CONTROL_SEND_TIMEOUT_CNT | INTEGER | 从节点拉起到目前为止控制通道发送超时累计次数 |
| CONTROL_RECV_TIMEOUT_CNT | INTEGER | 从节点拉起到目前为止控制通道接收超时累计次数 |
| DATA_SEND_TIMEOUT_CNT | INTEGER | 从节点拉起到目前为止数据通道发送超时累计次数 |
| DATA_RECV_TIMEOUT_CNT | INTEGER | 从节点拉起到目前为止数据通道接收超时累计次数 |
