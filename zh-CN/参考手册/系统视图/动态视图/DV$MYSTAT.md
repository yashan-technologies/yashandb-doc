本视图显示分布式集群中所有节点的当前会话的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 当前会话的ID |
| STATISTIC# | INTEGER | 统计项ID， 标识每一个统计项，可以通过STATISTIC#在V$STATNAME视图中查找对应统计项的具体名称 |
| VALUE | BIGINT | 统计值 |
