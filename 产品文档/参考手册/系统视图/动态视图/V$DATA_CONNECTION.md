本视图显示当前节点已创建的会话内连接信息。

| 字段  | 类型  | 说明                                                              |
| --- | --- |-----------------------------------------------------------------|
| GLOBAL_SESSION_ID | INTEGER | 分布式全局会话ID                                                       |
| HANDLER_ID | SMALLINT | 自身handler ID                                                    |
| SERIAL# | INTEGER | 分配序列号                                                           |
| SEQ_ID | INTEGER | sequence ID                                                     |
| SQL_ID | VARCHAR(13) | 当前会话正在执行的SQL ID（SQL文本的哈希/加密运算结果）                                |
| PEER_GROUP_ID | INTEGER | 对端组ID                                                           |
| PEER_GROUP_NODE_ID | INTEGER | 对端组内节点ID                                                        |
| PEER_ENDPOINT | SMALLINT | 对端节点通信ID                                                        |
| STATUS | VARCHAR(8) | 连接状态<br>\* IDLE：空闲<br>\* BUSY：忙碌<br>\* ERROR：错误<br>\* FAILED：失败 |
| WAIT_ACK_TIME | BIGINT | 等待回应时间                                                          |
| IS_CONNECTED | VARCHAR(8) | CN与对应节点是否连接 <br>\* YES：是<br>\* NO：否                             |
| IS_FIRST | VARCHAR(8) | 是否是第一次连接 <br>\* YES：是<br>\* NO：否                                |
| CONNECTION_VERSION | INTEGER | 使用ICS的连接版本                                                      |
| PEER_HANDLER_ID | INTEGER | 对端handler ID                                                    |
| PEER_HANDLER_SERIAL# | INTEGER | 对端handler序列号                                                    |