故障诊断视图，显示存算一体分布式集群中所有节点的事件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| INCIDENT\_ID | BIGINT | 事件ID |
| PROBLEM\_ID | INTEGER | 与事件关联的问题ID |
| SESSION\_ID | INTEGER | 创建事件的会话ID |
| CREATE\_TIME | DATE | 创建事件的日期和时间 |
| FLOOD\_CONTROL\_NUMBER | INTEGER | 防洪控制的事件个数 |
| ERROR\_NUMBER | INTEGER | 当前事件的错误号 |
| ERROR\_ARGUMENT | VARCHAR(128) | 与事件关联的错误参数 |
| ERROR\_COMMENTS | VARCHAR(256) | 事件的错误描述 |
| ERROR\_MESSAGE | VARCHAR(512) | 产生事件时数据库的错误信息 |
