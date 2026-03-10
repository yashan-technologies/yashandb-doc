故障诊断视图，显示当前所有的问题信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| PROBLEM_ID | INTEGER | 问题的ID |
| PROBLEM_KEY | VARCHAR(256) | 当前问题的问题键 |
| FIRST_INCIDENT | BIGINT | 显示当前问题的第一个事件ID |
| FIRSTINC_TIME | DATE | 显示当前问题发生第一个事件的时间戳 |
| LAST_INCIDENT | BIGINT | 显示当前问题的最后一个事件ID |
| LASTINC_TIME | DATE | 显示当前问题上次发生事件的时间戳 |
| CUMULATIVE_NUMBER | BIGINT | 显示当前问题的累计次数（不累计洪水控制的事件个数） |
