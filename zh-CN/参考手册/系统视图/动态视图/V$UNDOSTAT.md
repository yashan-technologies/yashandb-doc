本视图显示undo相关统计信息汇总。

|  字段| 类型| 说明|
| --- | --- | --- |
| ID  | INTEGER | undo segment ID |
| BLK_REUSE | INTEGER | 从blocklist复用undo block的次数 |
| STEAL | INTEGER | 尝试去其他segment窃取已过期block的次数 |
| DEGRADE_STEAL | INTEGER | 尝试去其他segment降级窃取未过期block的次数 |
| FORCE_STEAL | INTEGER | 尝试去其他segment强制窃取未过期block的次数 |
| STEALED | INTEGER | 被其他segment窃取已过期block的次数 |
| DEGRADE_STEALED | INTEGER | 被其他segment降级窃取未过期block的次数 |
| FORCE_STEALED | INTEGER | 被其他segment强制窃取未过期block的次数 |
| BALANCE_TIME | DATE | 上一次发生后台undo自动均衡优化的时间 |
| BALANCE | INTEGER | 发生后台undo自动均衡优化的次数 |
| BALANCE_BLK | INTEGER | undo自动均衡优化一共从segment归还给表空间的block个数 |
| RECYCLE_TIME | DATE | 上一次发生后台回收的时间 |
| RECYCLE_UFB | INTEGER | 后台回收未初始化block的次数 |
| RECYCLE_LIST | INTEGER | 后台回收blocklist的次数 |
| RECYCLE_LIST_BLK | INTEGER | 后台回收本segment上blocklist时总共回收的block个数 |
| RECYCLE_REQ | INTEGER | segment发起回收请求的次数 |
| GET_RECYCLED_LIST | INTEGER | segment发起回收请求后，回收其他segment的blocklist的次数 |
| GET_LIST_BLK | INTEGER | segment发起回收请求后，从其他segment的blocklist上回收到的block个数 |
