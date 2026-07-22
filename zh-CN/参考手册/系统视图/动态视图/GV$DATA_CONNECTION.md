本视图显示已创建的会话内连接信息。

|  字段| 类型| 说明|
| --- | --- |-----------------------------------------------------------------|
| GROUP_ID      | NUMBER     | 组ID                                                             |
| GROUP_NODE_ID | NUMBER     | 组内节点ID                                                          |
| INST_ID       | NUMBER      | 实例ID                                                            |
| GLOBAL\_SESSION\_ID | INTEGER | 分布式全局会话ID                                                       |
| HANDLER\_ID | SMALLINT | 自身handler ID                                                    |
| SERIAL# | INTEGER | 分配序列号                                                           |
| SEQ\_ID | INTEGER | sequence ID                                                     |
| SQL\_ID | VARCHAR(13) | 当前会话正在执行的SQL ID（SQL文本的哈希/加密运算结果）                                |
| PEER\_GROUP\_ID | INTEGER | 对端组ID                                                           |
| PEER\_GROUP\_NODE\_ID | INTEGER | 对端组内节点ID                                                        |
| PEER\_ENDPOINT | SMALLINT | 对端节点通信ID                                                        |
| STATUS | VARCHAR(8) | 连接状态<br>\* IDLE：空闲<br>\* BUSY：忙碌<br>\* ERROR：错误<br>\* FAILED：失败 |
| WAIT\_ACK\_TIME | BIGINT | 等待回应时间                                                          |
| IS\_CONNECTED | VARCHAR(8) | CN与对应节点是否连接 <br>\* YES：是<br>\* NO：否                             |
| IS\_FIRST | VARCHAR(8) | 是否是第一次连接 <br>\* YES：是<br>\* NO：否                                |
| CONNECTION\_VERSION | INTEGER | 使用ICS的连接版本                                                      |
| PEER\_HANDLER\_ID | INTEGER | 对端handler ID                                                    |
| PEER\_HANDLER\_SERIAL# | INTEGER | 对端handler序列号                                                    |
