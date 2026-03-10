本视图显示VM的统计信息 。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID | INTEGER | 当前会话的ID |
| ALLOC_COUNT | BIGINT | 累计分配的次数 |
| OPEN_COUNT | BIGINT | 累计打开的次数 |
| CLOSE_COUNT | BIGINT | 累计关闭的次数 |
| FREE_COUNT | BIGINT | 累计释放的次数 |
| SWAP_OUT_COUNT | BIGINT | 累计换出到磁盘的次数 |
| SWAP_IN_COUNT | BIGINT | 累计换入到内存的次数 |
| IO_WAIT_COUNT | BIGINT | 累计发生IO等待的次数 |
| EXTEND_COUNT | BIGINT | 累计发生VM页扩展的次数 |
| CURR_OPEN | INTEGER | 当前正在打开的页数 |
| CURR_CLOSE | INTEGER | 当前关闭记录的次数 |
| CURR_SWAP_OUT | INTEGER | 当前正在换出磁盘的页数 |
