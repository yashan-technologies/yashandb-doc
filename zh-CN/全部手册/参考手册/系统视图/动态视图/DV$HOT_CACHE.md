本视图显示存算一体分布式集群中所有节点热页缓存的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| FREE_COUNT | INTEGER | 当前可以使用的空闲热页缓存页个数 |
| GENERATE_COUNT | BIGINT | 数据库启动后分配出去的热页缓存页个数 |
| RECYCLE_COUNT | BIGINT | 数据库启动后回收回来的热页缓存页个数 |
| HWM | INTEGER | 当前数据库热页缓存页的高水位（高水位以后的热页缓存页还没有被分出去过） |
| TOTAL_COUNT | INTEGER | 数据库热页缓存页的总个数 |
| BLOCK_SIZE | INTEGER | 热页缓存页的单个页大小（单位：字节） |
