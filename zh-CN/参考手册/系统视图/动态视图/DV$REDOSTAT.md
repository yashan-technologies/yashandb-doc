本视图显示存算一体分布式集群中所有节点redo性能的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| REDO\_FLUSH\_SPEED  | BIGINT | 当前redo刷盘速度（最近3秒的平均速度）（单位：KB/s） |
| CHECKPOINT\_SPEED  | BIGINT | checkpoint推进redo回放点的当前速度（最近3秒的平均速度）（单位：KB/s） |
| AVR\_CHECKPOINT\_SPEED  | BIGINT | checkpoint推进redo回放点的平均速度（最近3000秒的平均速度）（单位：KB/s） |
| FREE\_SPACE\_SIZE  | BIGINT | redo文件的剩余空间（单位：字节） |
| TOTAL\_WRITE\_SIZE  | BIGINT | 数据库启动后，redo文件写入总大小（单位：字节） |
| BATCH\_COMMIT\_DELAY  | INTEGER | 参数COMMIT_LOGGING为BATCH的情况下，事务提交到触发redo刷盘的延迟时间（单位：us） |
