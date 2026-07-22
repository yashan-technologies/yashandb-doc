本视图显示并行执行任务的详细情况。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| ID        | TINYINT       | 任务的编号                       |
| NAME      | VARCHAR(68)   | 容器的名称                       |
| STATUS    | VARCHAR(32)   | 任务的运行状态：<br/> * SUCCESS：运行成功<br/> * RUNNING：正在运行<br/> * FAILED：任务执行失败 |
| TYPE      | VARCHAR(32)   | 任务的类型：<br/> * build database：构建数据库<br/> * switchover：执行计划内切换<br/> * failover：执行故障切换      |
| START_TIME | TIMESTAMP(6)  | 任务的启动时间                    |
| END_TIME   | TIMESTAMP(6)  | 任务的结束时间                    |
| ERROR      | VARCHAR(8192)  | 任务运行失败的错误信息描述 |
