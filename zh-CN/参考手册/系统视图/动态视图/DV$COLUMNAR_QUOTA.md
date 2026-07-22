本视图显示存算一体分布式集群中所有节点向量化计算过程全局使用的配额信息。

|  字段| 类型| 说明|
|--------------------|---------|-------------|
| GROUP\_ID          | INTEGER | 组ID         |
| GROUP\_NODE\_ID    | INTEGER | 组内节点ID      |
| TOTAL              | BIGINT  | 总共配额大小      |
| USED               | BIGINT  | 已使用的配额大小    |
| FREE\_UP\_LEVEL    | BIGINT  | 已分配配额的上限    |
| ACTIVE\_SQL\_COUNT | BIGINT  | 当前SQL语句的数量  |
| MAX\_USED          | BIGINT  | 已使用的最大配额    |
