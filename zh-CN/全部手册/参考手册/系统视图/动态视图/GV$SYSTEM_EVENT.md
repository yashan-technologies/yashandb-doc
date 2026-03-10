本视图显示当前所有等待事件统计信息。在[等待事件](../../等待事件)文档中可以查看具体等待事件的定义及说明。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| EVENT | VARCHAR(32) | 等待事件名称 |
| TOTAL_WAITS | BIGINT | 总等待次数 |
| TOTAL_TIMEOUTS | BIGINT | 总超时次数 |
| TIME_WAITED | BIGINT | 等待时间（单位：毫秒） |
| AVERAGE_WAIT | NUMBER | 平均等待时间（单位：毫秒） |
| TIME_WAITED_MICRO | BIGINT | 等待时间（单位：微秒） |
| TOTAL_WAITS_FG | BIGINT | 前台等待次数 |
| TOTAL_TIMEOUTS_FG | BIGINT | 前台超时次数 |
| TIME_WAITED_FG | BIGINT | 前台等待时间 （单位：毫秒） |
| AVERAGE_WAIT_FG | NUMBER | 前台平均等待时间（单位：毫秒） |
| TIME_WAITED_MICRO_FG | BIGINT | 前台等待时间（单位：微秒） |
| EVENT_ID | INTEGER | 等待事件ID |
| WAIT_CLASS | VARCHAR(16) | 等待事件类别，来源于[V$SYSTEM_WAIT_CLASS](./V$SYSTEM_WAIT_CLASS)的WAIT_CLASS字段值 |
