本视图用于查询存算一体分布式集群中所有节点CM模块内存储的TASK INFO信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| TASK\_TYPE | VARCHAR(16) | 任务名字 |
| SEND\_TIMES | INTEGER | 发送次数 |
| TASK\_INFO | VARCHAR(256) | 任务消息 |
| TARGET\_NODES | VARCHAR(128) | 目标节点 |
