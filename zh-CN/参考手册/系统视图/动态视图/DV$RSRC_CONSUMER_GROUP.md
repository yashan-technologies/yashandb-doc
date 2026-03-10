本视图显示分布式集群中所有节点资源使用组相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID | BIGINT | 资源组ID |
| NAME | VARCHAR(68) | 资源组名称 |
| SPA_LIMIT_QUOTA | BIGINT | 资源组内存配额大小（单位：字节） |
| SESSION_SPA_LIMIT_QUOTA | BIGINT | 会话内存配额大小（单位：字节） |
| SPA_REMAIN_QUOTA | BIGINT | 资源组内存配额剩余大小（单位：字节） |
| SPA_MAX_USE_QUOTA | BIGINT | 资源组内存使用最大配额大小（单位：字节） |
| SESSION_SPA_RESERVED_QUOTA | BIGINT | 预留的内存配额，该范围内无需向资源组申请（单位：字节） |
| SPA_LIMIT_EXCEED_TIMES | INTEGER | 资源组内存达到上限的次数 |
| SESSION_SPA_LIMIT_EXCEED_TIMES | INTEGER | 会话内存使用达到上限的次数 |
| SESSION_SPA_MAX_USE_MEM | BIGINT | 会话内存使用最大值（单位：字节） |
| CONCURRENCY_LIMIT | INTEGER | 资源计划配置最大并发数 |
| CONCURRENCY_NUM | INTEGER | 当前正在执行的资源密集型SQL数量 |
| EXECUTION_WAITERS | INTEGER | 等待执行的资源密集型SQL |
| REQUESTS | INTEGER | 资源组内已经执行的资源密集型SQL数量 |
| QUEUE_NUMBER | INTEGER | 排队叫号值 |
| CONCURRENCY_LIMIT_HIT | INTEGER | 同时执行资源密集型SQL数量达到上限次数 |
| QUEUED_TIME | BIGINT | 资源组内所有会话累计等待执行时间 |
| QUEUE_TIMEOUTS | INTEGER | 资源组内会话累计等待调度超时次数 |
| PARALLEL_DOWNGRADE_TIMES | INTEGER | 资源组内出现并行资源降级次数 |
