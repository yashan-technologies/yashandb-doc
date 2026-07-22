本视图显示共享集群消息池中待处理的消息信息 。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| SRC\_INSTANCE | INTEGER | 消息的发送节点ID |
| SRC\_SID | INTEGER | 消息的发送会话ID |
| DST\_INSTANCE | INTEGER | 消息的接收节点ID |
| DST\_SID | INTEGER | 消息的接收会话ID |
| NAME | VARCHAR(32) | 消息名称 |
| SERIAL\_NO | INTEGER | 消息序列号 |
| SIZE | INTEGER | 消息大小 |
| IN\_PROCESS | BOOLEAN | 是否正在处理 |
| TASK\_SID | INTEGER | 处理消息的后台会话ID |
