本视图用于检测VM使用情况。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID  | SMALLINT | 分区ID |
| TOTAL_BLOCKS | INTEGER | 总的内存页数目 |
| FREE_BLOCKS | INTEGER | 处于空闲状态的内存页数目 |
| OPENED_BLOCKS | INTEGER | 处于打开状态的内存页数目 |
| CLOSED_BLOCKS | INTEGER | 处于关闭状态的内存页数目 |
| SWAPPED_OUT_BLOCKS | INTEGER | 换出磁盘的内存页数目 |
| CTRL_BLOCKS | INTEGER | 控制页面的数目 |

