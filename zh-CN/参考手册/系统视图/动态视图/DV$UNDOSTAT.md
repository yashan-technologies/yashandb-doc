本视图显示存算一体分布式集群中所有节点的undo相关统计信息汇总。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | undo segment ID |
| BLK\_REUSE | INTEGER | 从blocklist复用undo block的次数 |
| STEAL | INTEGER | 尝试去其他segment窃取已过期block的次数 |
| DEGRADE\_STEAL | INTEGER | 尝试去其他segment降级窃取未过期block的次数 |
| FORCE\_STEAL | INTEGER | 尝试去其他segment强制窃取未过期block的次数 |
| STEALED | INTEGER | 被其他segment窃取已过期block的次数 |
| DEGRADE\_STEALED | INTEGER | 被其他segment降级窃取未过期block的次数 |
| FORCE\_STEALED | INTEGER | 被其他segment强制窃取未过期block的次数 |
| BALANCE\_TIME | DATE | 上一次发生后台undo自动均衡优化的时间 |
| BALANCE | INTEGER | 发生后台undo自动均衡优化的次数 |
| BALANCE\_BLK | INTEGER | undo自动均衡优化一共从segment归还给表空间的block个数 |
| RECYCLE\_TIME | DATE | 上一次发生后台回收的时间 |
| RECYCLE\_UFB | INTEGER | 后台回收未初始化block的次数 |
| RECYCLE\_LIST | INTEGER | 后台回收blocklist的次数 |
| RECYCLE\_LIST\_BLK | INTEGER | 后台回收本segment上blocklist时总共回收的block个数 |
| RECYCLE\_REQ | INTEGER | segment发起回收请求的次数 |
| GET\_RECYCLED\_LIST | INTEGER | segment发起回收请求后，回收其他segment的blocklist的次数 |
| GET\_LIST\_BLK | INTEGER | segment发起回收请求后，从其他segment的blocklist上回收到的block个数 |
| BLK\_APPEND | INTEGER | 从free array复用undo block的次数|
| BLK\_ALLOC | INTEGER| 从undo表空间申请到undo block的数量|
| BLK\_REHANG | INTEGER | 从free array移到blocklist的undo block的数量 |
| XACT\_AVG\_SIZE | BIGINT | 事务平均消耗的undo字节数 |
