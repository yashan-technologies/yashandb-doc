本视图显示存算一体分布式集群中所有节点系统事件统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
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
