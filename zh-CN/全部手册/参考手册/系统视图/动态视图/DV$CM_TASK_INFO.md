本视图用于查询存算一体分布式集群中所有节点CM模块内存储的TASK INFO信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| TASK_TYPE | VARCHAR(16) | 任务名字 |
| SEND_TIMES | INTEGER | 发送次数 |
| TASK_INFO | VARCHAR(256) | 任务消息 |
| TARGET_NODES | VARCHAR(128) | 目标节点 |
