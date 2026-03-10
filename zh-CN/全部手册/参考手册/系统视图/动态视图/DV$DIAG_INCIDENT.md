故障诊断视图，显示存算一体分布式集群中所有节点的事件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| INCIDENT_ID | BIGINT | 事件ID |
| PROBLEM_ID | INTEGER | 与事件关联的问题ID |
| SESSION_ID | INTEGER | 创建事件的会话ID |
| CREATE_TIME | DATE | 创建事件的日期和时间 |
| FLOOD_CONTROL_NUMBER | INTEGER | 防洪控制的事件个数 |
| ERROR_NUMBER | INTEGER | 当前事件的错误号 |
| ERROR_ARGUMENT | VARCHAR(128) | 与事件关联的错误参数 |
| ERROR_COMMENTS | VARCHAR(256) | 事件的错误描述 |
| ERROR_MESSAGE | VARCHAR(512) | 产生事件时数据库的错误信息 |
