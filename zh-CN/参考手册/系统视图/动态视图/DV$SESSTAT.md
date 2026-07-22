本视图显示存算一体分布式集群中所有会话的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| STATISTIC# | INTEGER | 统计项ID， 标识每一个统计项，可以通过STATISTIC#在V$STATNAME视图中查找对应统计项的具体名称 |
| VALUE | BIGINT | 统计值 |
