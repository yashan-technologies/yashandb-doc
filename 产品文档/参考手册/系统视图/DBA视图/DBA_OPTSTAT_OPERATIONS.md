本视图显示使用DBMS_STATS高级包和数据库级别执行的统计信息收集操作的历史记录，不适用于分布式部署。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| ID | BIGINT | 统计信息收集操作的内部ID |
| OPERATION | VARCHAR(64) | 统计信息收集操作的名称 |
| TARGET | VARCHAR(512) | 统计信息收集目标的名称 |
| START_TIME | TIMESTAMP | 开始时间 |
| END_TIME | TIMESTAMP | 结束时间 |
| STATUS | VARCHAR(11) | 统计信息收集操作的状态<br>\*   IN PROGRESS：正在运行<br>\*   COMPLETED：完成 <br>\*   FAILED：失败 |
| SESSION_ID | INTEGER | 触发当次统计信息收集操作的会话ID |
| NOTES | VARCHAR(4000) | 统计信息收集操作的注释，例如统计信息选项值和状态为FAILED的操作的失败信息 |
