本视图显示存算一体分布式集群中所有节点的日志回放进度汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| ITEM | VARCHAR(32) | 统计项的名称 |
| UNITS | VARCHAR(16) | 统计项的单位 |
| VALUE | BIGINT | 统计项的值 |

统计项信息：

|  统计项| 单位| 描述|
| --- | --- | --- |
| Active Apply Rate | KB/sec | 当前回放速度 |
| Average Apply Rate | KB/sec | 平均回放速度 |
| Maximum Apply Rate | KB/sec | 最大回放速度 |
| Redo Applied | MB  | 已回放的日志量 |
| Redo Remain | MB  | 待回放的日志量，如果为-1，说明归档日志存在gap |
| First Applied Redo | LFN | 回放的第一个日志的LFN号（日志刷盘序号） |
| Last Applied Redo | LFN | 回放的最后一个日志的LFN号（日志刷盘序号） |
| Active Time | Seconds | 日志回放消耗的时间（没有日志需要回放时，该时间不会增加） |
| Remain Time | Seconds | 剩余日志需要的回放时间（预估值，当redo日志回放完毕时为0） |
| Auxiliary Log Count | Number | 并行回放时，DDL等辅助日志的个数，该项增多会影响并行回放性能 |
| Parallelism | Threads | 并行回放线程数，不开启并行回放时值为0，开启时只能为2的幂次方 |
| Latest Load Size | KB | 最近一次日志读取的大小 |
| Latest Load Time | Millisecond | 最近一次日志读取的耗时 |
| Latest Analysis Time | Millisecond | 最近一次日志分析的耗时 |
| Wait Previous Apply Time | Millisecond | 等待上一次回放结束的耗时 |
| Latest Apply Time | Millisecond | 最近一次日志回放的总时间 |

数据库从MOUNT到OPEN阶段，统计的是重启回放信息，视图中Redo Remain项随着回放会减小。主库OPEN后，视图项不再变化。但是备库OPEN后，可能会重置视图内容，并且Redo Remain项和Remain Time项表示当前剩余日志对应的大小和回放时间。
