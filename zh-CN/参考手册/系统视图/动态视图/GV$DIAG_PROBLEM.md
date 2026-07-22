故障诊断视图，显示当前所有的问题信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| PROBLEM\_ID | INTEGER | 问题的ID |
| PROBLEM\_KEY | VARCHAR(256) | 当前问题的问题键 |
| FIRST\_INCIDENT | BIGINT | 显示当前问题的第一个事件ID |
| FIRSTINC\_TIME | DATE | 显示当前问题发生第一个事件的时间戳 |
| LAST\_INCIDENT | BIGINT | 显示当前问题的最后一个事件ID |
| LASTINC\_TIME | DATE | 显示当前问题上次发生事件的时间戳 |
| CUMULATIVE\_NUMBER | BIGINT | 显示当前问题的累计次数（不累计洪水控制的事件个数） |
