本视图显示当前所有等待事件类的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| WAIT\_CLASS\_ID | BIGINT | 等待事件类ID |
| WAIT\_CLASS# | BIGINT | 等待事件类号 |
| WAIT\_CLASS | VARCHAR(32) | 等待事件类名字 |
| TOTAL\_WAITS | BIGINT | 总共等待次数 |
| TIME\_WAITED | BIGINT | 总共等待时间（单位：微秒） |
| TOTAL\_WAITS\_FG | BIGINT | 前台等待次数 |
| TIME\_WAITED\_FG | BIGINT | 前台等待时间（单位：微秒） |
