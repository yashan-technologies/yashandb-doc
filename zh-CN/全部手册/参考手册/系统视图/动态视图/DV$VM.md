本视图显示存算一体分布式集群中所有节点的VM的整体内存信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID  | SMALLINT | 分区ID |
| TOTAL_BLOCKS | INTEGER | 总的内存页数目 |
| FREE_BLOCKS | INTEGER | 处于空闲状态的内存页数目 |
| OPENED_BLOCKS | INTEGER | 处于打开状态的内存页数目 |
| CLOSED_BLOCKS | INTEGER | 处于关闭状态的内存页数目 |
| SWAPPED_OUT_BLOCKS | INTEGER | 换出磁盘的内存页数目 |
| CTRL_BLOCKS | INTEGER | 控制页面的数目 |
| FREE_SWAP_BLOCKS | INTEGER | 可用于换出的空闲页面总数，存在不同分区时，每个分区显示的都是所有分区可换出的空闲页面总数 |
