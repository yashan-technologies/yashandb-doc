本视图显示并行worker池中的worker信息。

| 字段                     | 类型         | 说明  |
| ---                     | ---         | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME                    | VARCHAR(32) | worker名 |
| WORKER_ID              | INTEGER     | worker ID |
| THREAD_ID              | BIGINT      | 操作系统线程ID |
| QUEUE_ID               | INTEGER     | worker的任务队列ID |
| POOL_ID                | INTEGER     | worker池ID |
| TASK_ADDR              | BIGINT      | worker正在执行的任务地址 |
| STATUS                  | VARCHAR(8)  | worker状态<br>\* IDLE<br>\* IN USE |
| SID                     | SMALLINT    | 如果有并行任务在执行，则为会话ID |
| SERIAL#                 | INTEGER     | 会话的序列号 |
| RUN_TASK_TIMES        | BIGINT      | 执行并行任务的次数 |
| WORKER_START_TIME     | TIMESTAMP   | worker的启动时间 |
| LAST_ACTIVE_TIME      | TIMESTAMP   | 上次活跃时间，用于判定线程是否卡住 |
| LAST_TASK_START_TIME | TIMESTAMP   | 执行最近一次并行任务的启动时间 |
| LAST_TASK_END_TIME   | TIMESTAMP   | 执行最近一次并行任务的结束时间 |
| MAX_TASK_RUN_TIME    | BIGINT      | 所有并行任务中最长的一次执行时间（单位：μs微秒） |
| MIN_TASK_RUN_TIME    | BIGINT      | 所有并行任务中最短的一次执行时间（单位：μs微秒） |
| AVG_TASK_RUN_TIME    | BIGINT      | 所有并行任务中的平均执行时间（单位：μs微秒） |