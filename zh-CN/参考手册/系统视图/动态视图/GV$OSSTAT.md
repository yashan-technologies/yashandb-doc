本视图显示集群中所有实例来自操作系统的系统利用率统计信息 。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| STAT_NAME | VARCHAR(64) | 统计名称 |
| VALUE | BIGINT | 瞬时统计值 |
| OSSTAT_ID | INTEGER | 统计编号 |
| COMMENTS | VARCHAR(64) | 对统计数据的任何其他特定于操作系统的说明 |
| CUMULATIVE | VARCHAR(3) | 指示统计量是否是累积的（即随时间累积）<br/>* YES<br/>* NO |
