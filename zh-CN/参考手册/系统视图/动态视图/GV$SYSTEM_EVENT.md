本视图显示当前所有等待事件统计信息。在[等待事件](../../等待事件)文档中可以查看具体等待事件的定义及说明。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| EVENT | VARCHAR(32) | 等待事件名称 |
| TOTAL\_WAITS | BIGINT | 总等待次数 |
| TOTAL\_TIMEOUTS | BIGINT | 总超时次数 |
| TIME\_WAITED | BIGINT | 等待时间（单位：毫秒） |
| AVERAGE\_WAIT | NUMBER | 平均等待时间（单位：毫秒） |
| TIME\_WAITED\_MICRO | BIGINT | 等待时间（单位：微秒） |
| TOTAL\_WAITS\_FG | BIGINT | 前台等待次数 |
| TOTAL\_TIMEOUTS\_FG | BIGINT | 前台超时次数 |
| TIME\_WAITED\_FG | BIGINT | 前台等待时间 （单位：毫秒） |
| AVERAGE\_WAIT\_FG | NUMBER | 前台平均等待时间（单位：毫秒） |
| TIME\_WAITED\_MICRO\_FG | BIGINT | 前台等待时间（单位：微秒） |
| EVENT\_ID | INTEGER | 等待事件ID |
| WAIT\_CLASS | VARCHAR(16) | 等待事件类别，来源于[V$SYSTEM_WAIT_CLASS](./V$SYSTEM_WAIT_CLASS)的WAIT_CLASS字段值 |
