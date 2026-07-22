本视图显示存算一体分布式集群中所有节点的数据缓存区基本信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | 缓存区分区编号 |
| SIZE | BIGINT | 缓存区分区大小（单位：字节） |
| BLOCK\_SIZE | INTEGER | 缓存区分区的数据块大小（单位：字节） |
| CHUNKS | INTEGER | 数据块分区数据chunk的数量 |
| BUFFERS | INTEGER | 数据块分区数据块的数量 |
