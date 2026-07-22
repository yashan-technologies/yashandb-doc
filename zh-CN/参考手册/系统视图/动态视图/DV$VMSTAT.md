本视图显示存算一体分布式集群中所有节点的VM的统计信息。 

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| SID | INTEGER | 当前会话的ID |
| ALLOC\_COUNT | BIGINT | 累计分配的次数 |
| OPEN\_COUNT | BIGINT | 累计打开的次数 |
| CLOSE\_COUNT | BIGINT | 累计关闭的次数 |
| FREE\_COUNT | BIGINT | 累计释放的次数 |
| SWAP\_OUT\_COUNT | BIGINT | 累计换出到磁盘的次数 |
| SWAP\_IN\_COUNT | BIGINT | 累计换入到内存的次数 |
| IO\_WAIT\_COUNT | BIGINT | 累计发生IO等待的次数 |
| EXTEND\_COUNT | BIGINT | 累计发生VM页扩展的次数 |
| CURR\_OPEN | INTEGER | 当前正在打开的页数 |
| CURR\_CLOSE | INTEGER | 当前关闭记录的次数 |
| CURR\_SWAP\_OUT | INTEGER | 当前正在换出磁盘的页数 |
