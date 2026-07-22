本视图显示共享模式打开时，使用会话线程池和登录线程池的汇总信息。

|  字段| 类型| 说明|
|------------------|---------|---------------------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| WORKER_ID        | SMALLINT | 当前worker ID       |
| THREAD_ID        | BIGINT | worker使用的线程号        |
| QUEUE_ID       | INTEGER  | 当前worker使用的队列ID     |
| POOL_ID       | INTEGER  | 当前worker池的ID     |
| TASK_ADDR  | BIGINT  | worker中正在执行的任务的地址 |
| GET_TASK_TIMES  | BIGINT  | worker从队列中拿任务的次数（可能会失败）    |
| GET_TASK_TIMEOUT_TIMES | BIGINT | worker从队列中拿任务超时没拿到的次数 |
| RUN_TASK_TIMES | BIGINT | worker执行过的任务数 |
| STEAL_TASK_TIMES | BIGINT | 尝试从其他任务队列获取任务的次数 |
| RUN_STEAL_TASK_TIMES | BIGINT | 从其他任务队列获取任务的次数 |
| WORKER_START_TIME | TIMESTAMP(6) | worker线程创建的时间 |
| LAST_ACTIVE_TIME | TIMESTAMP(6) | worker上次活跃的时间 |
| LAST_TASK_START_TIME | TIMESTAMP(6) | worker上次注册任务后开始执行任务的时间 |
| LAST_TASK_END_TIME | TIMESTAMP(6) | worker上次注册任务后结束执行的时间，若当前未结束则为NULL |
| MAX_TASK_RUN_TIME | BIGINT | worker执行任务的最长时间（单位：毫秒） |
| MIN_TASK_RUN_TIME | BIGINT | worker执行任务的最短时间（单位：毫秒） |
| AVG_TASK_RUN_TIME | BIGINT | worker任务平均执行时间（单位：毫秒） |
