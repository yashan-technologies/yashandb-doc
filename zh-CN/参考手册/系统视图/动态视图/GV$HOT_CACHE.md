本视图用于查看数据库中当前热页缓存区的使用情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| FREE\_COUNT | INTEGER | 当前可以使用的空闲热页缓存页个数 |
| GENERATE\_COUNT | BIGINT | 数据库启动后分配出去的热页缓存页个数 |
| RECYCLE\_COUNT | BIGINT | 数据库启动后回收回来的热页缓存页个数 |
| HWM | INTEGER | 当前数据库BTree缓存页的高水位（高水位以后的热页缓存页还没有被分出去过） |
| TOTAL\_COUNT | INTEGER | 数据库热页缓存页的总个数 |
| BLOCK\_SIZE | INTEGER | 热页缓存页的单个页大小（单位：字节） |
