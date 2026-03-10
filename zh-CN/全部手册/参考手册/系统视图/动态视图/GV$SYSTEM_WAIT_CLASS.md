本视图显示当前所有等待事件类的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| WAIT_CLASS_ID | BIGINT | 等待事件类ID |
| WAIT_CLASS# | BIGINT | 等待事件类号 |
| WAIT_CLASS | VARCHAR(32) | 等待事件类名字 |
| TOTAL_WAITS | BIGINT | 总共等待次数 |
| TIME_WAITED | BIGINT | 总共等待时间（单位：微秒） |
| TOTAL_WAITS_FG | BIGINT | 前台等待次数 |
| TIME_WAITED_FG | BIGINT | 前台等待时间（单位：微秒） |
