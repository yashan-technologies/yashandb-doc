故障诊断视图，显示存算一体分布式集群中所有节点的问题信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| PROBLEM_ID | INTEGER | 问题的ID |
| PROBLEM_KEY | VARCHAR(256) | 当前问题的问题键 |
| FIRST_INCIDENT | BIGINT | 显示当前问题的第一个事件ID |
| FIRSTINC_TIME | DATE | 显示当前问题发生第一个事件的时间戳 |
| LAST_INCIDENT | BIGINT | 显示当前问题的最后一个事件ID |
| LASTINC_TIME | DATE | 显示当前问题上次发生事件的时间戳 |
| CUMULATIVE_NUMBER | BIGINT | 显示当前问题的累计次数（不累计洪水控制的事件个数） |
