本视图显示并行worker池中的worker信息。

|  字段| 类型| 说明|
| ---                     | ---         | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME                    | VARCHAR(32) | worker名 |
| WORKER\_ID              | INTEGER     | worker ID |
| THREAD\_ID              | BIGINT      | 操作系统线程ID |
| QUEUE\_ID               | INTEGER     | worker的任务队列ID |
| POOL\_ID                | INTEGER     | worker池ID |
| TASK\_ADDR              | BIGINT      | worker正在执行的任务地址 |
| STATUS                  | VARCHAR(8)  | worker状态<br>\* IDLE<br>\* IN USE |
| SID                     | SMALLINT    | 如果有并行任务在执行，则为会话ID |
| SERIAL#                 | INTEGER     | 会话的序列号 |
| RUN\_TASK\_TIMES        | BIGINT      | 执行并行任务的次数 |
| WORKER\_START\_TIME     | TIMESTAMP(6)   | worker的启动时间 |
| LAST\_ACTIVE\_TIME      | TIMESTAMP(6)   | 上次活跃时间，用于判定线程是否卡住 |
| LAST\_TASK\_START\_TIME | TIMESTAMP(6)   | 执行最近一次并行任务的启动时间 |
| LAST\_TASK\_END\_TIME   | TIMESTAMP(6)   | 执行最近一次并行任务的结束时间 |
| MAX\_TASK\_RUN\_TIME    | BIGINT      | 所有并行任务中最长的一次执行时间（单位：μs微秒） |
| MIN\_TASK\_RUN\_TIME    | BIGINT      | 所有并行任务中最短的一次执行时间（单位：μs微秒） |
| AVG\_TASK\_RUN\_TIME    | BIGINT      | 所有并行任务中的平均执行时间（单位：μs微秒） |
