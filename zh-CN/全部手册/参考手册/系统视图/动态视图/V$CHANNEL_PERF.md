本视图用于查询当前会话各1对1channel的传输性能统计。

|  字段| 类型| 说明|
| --- | --- | --- |
| TQ_ID | SMALLINT | 表队列编号 |
| CHANNEL_ID | INTEGER | 通道编号 |
| DIRECTION | TINYINT | 方向，0或1 |
| SRC_PORT | SMALLINT | 发送端口号 |
| DST_PORT | SMALLINT | 接收端口号 |
| SRC_ENDPOINT | SMALLINT | 发送节点 |
| DST_ENDPOINT | SMALLINT | 接收节点 |
| ACTIVE_TIME | INTEGER | 激活时间（单位：毫秒） |
| SEND_BYTES | BIGINT | 采样时间内发送的数据量（单位：byte） |
| SEND_PACKETS | INTEGER | 采样时间内发送包的个数 |
| SEND_ACKS | INTEGER | 采样时间内发送ack的个数 |
| SEND_BUFFER_SIZE | INTEGER | 采样时发送缓存的大小（单位：byte） |
| SEND_WIND | INTEGER | 采样时发送窗口的大小，单位包的个数 |
| CONG_WIND | INTEGER | 采样时拥塞窗口的大小，单位包的个数 |
| SLOW_STARTS | INTEGER | 采样时滑动窗口重启次数（单位：次） |
| SEND_WIND_LIMITED | INTEGER | 采样时间内因发送窗口限制而等待的次数 |
| CONG_WIND_LIMITED | INTEGER | 采样时间内因拥塞窗口限制而等待的次数 |
| WAIT_SPACE_TIMES | INTEGER | 采样时间内因发送缓存不足而等待的次数 |
| WAIT_SPACE_TIMEOUTS | INTEGER | 采样时间内因发送缓存不足而等待超时的次数 |
| ROUND_TRIP_TIME | INTEGER | 报文处理一个来回的评估时间 |
| BANDWIDTH | FLOAT | 带宽（单位：M/s） |
| RECV_BYTES | BIGINT | 采样时间内接收的数据量（单位：byte） |
| RECV_PACKETS | INTEGER | 采样时间内接收包的个数 |
| RECV_ACKS | INTEGER | 采样时间内接收ack的个数 |
| RECV_BUFFER_SIZE | INTEGER | 采样时接收缓存的大小（单位：byte） |
| RECV_WIND | INTEGER | 采样时接收窗口的大小，单位包的个数 |
| WAIT_DATA_TIMES | INTEGER | 采样时间内接收端因数据未到来而等待的次数 |
| WAIT_DATA_TIMEOUTS | INTEGER | 采样时间内接收端因数据未到来而等待超时的次数 |
