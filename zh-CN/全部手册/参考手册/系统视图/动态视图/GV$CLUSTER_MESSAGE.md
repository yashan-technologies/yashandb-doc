本视图显示共享集群消息池中待处理的消息信息 。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SRC_INSTANCE | INTEGER | 消息的发送节点ID |
| SRC_SID | INTEGER | 消息的发送会话ID |
| DST_INSTANCE | INTEGER | 消息的接收节点ID |
| DST_SID | INTEGER | 消息的接收会话ID |
| NAME | VARCHAR(32) | 消息名称 |
| SERIAL_NO | INTEGER | 消息序列号 |
| SIZE | INTEGER | 消息大小 |
| IN_PROCESS | BOOLEAN | 是否正在处理 |
| TASK_SID | INTEGER | 处理消息的后台会话ID |
