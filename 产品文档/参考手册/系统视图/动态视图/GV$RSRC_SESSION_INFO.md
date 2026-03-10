本视图显示会话使用资源相关信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID | SMALLINT | 会话ID |
| CURRENT_CONSUMER_GROUP | VARCHAR(68) | 资源使用组名 |
| SPA_USE_MEM | BIGINT | 会话当前使用SPA内存大小，单位字节 |
| SPA_USE_QUOTA | BIGINT | 会话当前占有的配额，单位字节 |
| SPA_MAX_USE_MEM | BIGINT | 会话最大使用SPA内存大小，单位字节 |
| STATE | VARCHAR(32) | 会话状态：RUNNING 执行中，QUEUED 排队中，IDLE 当前没有命令执行，WAITING 等待事件 |
| QUEUE_ID | INTEGER | 排队序列号 |
| PRIORITY | INTEGER | 会话当前在队列中的权重 |
| CURRENT_QUEUED_TIME | BIGINT | 在队列中等待时间（状态为QUEUED），单位毫秒 |
| QUEUED_TIME | BIGINT | 会话累计等待时间（毫秒） |
| QUEUE_TIMEOUTS | INTEGER | 会话累计等待调度超时次数 |
| PARALLEL_DOWNGRADE_TIMES | INTEGER | 会话内出现并行资源降级次数 |

