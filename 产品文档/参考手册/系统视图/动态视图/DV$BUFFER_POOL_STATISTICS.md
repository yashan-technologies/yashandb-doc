本视图显示分布式集群中所有节点的数据缓存区的统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | 缓存区分区的编号 |
| SIZE | BIGINT | 缓存区分区的大小（单位：字节） |
| NUM_TOTAL | INTEGER | 数据块总数 |
| NUM_RESIDENT | INTEGER | 常驻内存数据块数量 |
| NUM_MAIN | INTEGER | 热块链数据块数量 |
| NUM_AUXILLIARY | INTEGER | 辅助链数据块数量 |
| NUM_WRITE | INTEGER | 脏页链数据块数量 |
| NUM_TEMP | INTEGER | 临时链数据块数量 |
