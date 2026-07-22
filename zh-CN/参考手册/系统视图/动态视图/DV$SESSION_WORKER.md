本视图显示存算一体分布式集群中所有节点分布式执行使用session worker池的汇总信息。

|  字段| 类型| 说明|
|------------------|---------|---------------------|
| GROUP\_ID         | INTEGER | 组ID |
| GROUP\_NODE\_ID    | INTEGER | 组内节点ID |
| WORKER\_ID        | SMALLINT | 当前worker ID       |
| THREAD\_ID        | BIGINT| worker使用的线程号        |
| QUEUE\_ID       | INTEGER  | 当前worker使用的队列ID     |
| POOL\_ID       | INTEGER  | 当前worker池的ID     |
| TASK\_ADDR  | BIGINT  | worker中正在执行的任务的地址 |
| GET\_TASK\_TIMES  | BIGINT  | worker从队列中拿任务的次数（可能会失败）    |
| GET\_TASK\_TIMEOUT\_TIMES | BIGINT | worker从队列中拿任务超时没拿到的次数 |
| RUN\_TASK\_TIMES | BIGINT | worker执行过的任务数 |
| STEAL\_TASK\_TIMES | BIGINT | 尝试从其他任务队列获取任务的次数 |
| RUN\_STEAL\_TASK\_TIMES | BIGINT | 从其他任务队列获取任务的次数 |
| WORKER\_START\_TIME | TIMESTAMP(6) | worker线程创建的时间 |
| LAST\_ACTIVE\_TIME | TIMESTAMP(6) | worker上次活跃的时间 |
| LAST\_TASK\_START\_TIME | TIMESTAMP(6) | worker上次注册任务后开始执行任务的时间 |
| LAST\_TASK\_END\_TIME | TIMESTAMP(6) | worker上次注册任务后结束执行的时间，若当前未结束则为NULL |
| MAX\_TASK\_RUN\_TIME | BIGINT | worker执行任务的最长时间（单位：微秒） |
| MIN\_TASK\_RUN\_TIME | BIGINT | worker执行任务的最短时间（单位：微秒） |
| AVG\_TASK\_RUN\_TIME | BIGINT | worker任务平均执行时间（单位：微秒） |
