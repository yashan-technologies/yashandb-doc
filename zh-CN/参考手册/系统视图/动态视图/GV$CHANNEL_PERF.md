本视图用于查询当前会话各1对1channel的传输性能统计。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| TQ\_ID | SMALLINT | 表队列编号 |
| CHANNEL\_ID | INTEGER | 通道编号 |
| DIRECTION | TINYINT | 方向，0或1 |
| SRC\_PORT | SMALLINT | 发送端口号 |
| DST\_PORT | SMALLINT | 接收端口号 |
| SRC\_ENDPOINT | SMALLINT | 发送节点 |
| DST\_ENDPOINT | SMALLINT | 接收节点 |
| ACTIVE\_TIME | INTEGER | 激活时间（单位：毫秒） |
| SEND\_BYTES | BIGINT | 采样时间内发送的数据量（单位：byte） |
| SEND\_PACKETS | INTEGER | 采样时间内发送包的个数 |
| SEND\_ACKS | INTEGER | 采样时间内发送ack的个数 |
| SEND\_BUFFER\_SIZE | INTEGER | 采样时发送缓存的大小（单位：byte） |
| SEND\_WIND | INTEGER | 采样时发送窗口的大小，单位包的个数 |
| CONG\_WIND | INTEGER | 采样时拥塞窗口的大小，单位包的个数 |
| SLOW\_STARTS | INTEGER | 采样时滑动窗口重启次数（单位：次） |
| SEND\_WIND\_LIMITED | INTEGER | 采样时间内因发送窗口限制而等待的次数 |
| CONG\_WIND\_LIMITED | INTEGER | 采样时间内因拥塞窗口限制而等待的次数 |
| WAIT\_SPACE\_TIMES | INTEGER | 采样时间内因发送缓存不足而等待的次数 |
| WAIT\_SPACE\_TIMEOUTS | INTEGER | 采样时间内因发送缓存不足而等待超时的次数 |
| ROUND\_TRIP\_TIME | INTEGER | 报文处理一个来回的评估时间 |
| BANDWIDTH | FLOAT | 带宽（单位：M/s） |
| RECV\_BYTES | BIGINT | 采样时间内接收的数据量（单位：byte） |
| RECV\_PACKETS | INTEGER | 采样时间内接收包的个数 |
| RECV\_ACKS | INTEGER | 采样时间内接收ack的个数 |
| RECV\_BUFFER\_SIZE | INTEGER | 采样时接收缓存的大小（单位：byte） |
| RECV\_WIND | INTEGER | 采样时接收窗口的大小，单位包的个数 |
| WAIT\_DATA\_TIMES | INTEGER | 采样时间内接收端因数据未到来而等待的次数 |
| WAIT\_DATA\_TIMEOUTS | INTEGER | 采样时间内接收端因数据未到来而等待超时的次数 |
