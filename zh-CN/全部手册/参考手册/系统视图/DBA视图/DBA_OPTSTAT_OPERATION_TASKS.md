本视图显示使用DBMS_STATS高级包和数据库级别执行的统计信息收集操作的历史记录，不适用于存算一体分布式集群部署。

|  字段| 类型| 说明|
| --- | --- | --- |
| OPID | BIGINT | 统计信息收集操作的内部ID |
| TARGET | VARCHAR(512) | 统计信息收集目标的名称 |
| TARGET_OBJ | BIGINT | 收集目标的ID |
| TARGET_TYPE | VARCHAR(15) | 收集目标的类型 |
| TARGET_SIZE | BIGINT | 收集任务开始时目标的大小（块数量） |
| START_TIME | TIMESTAMP(6) | 开始时间 |
| END_TIME | TIMESTAMP(6) | 结束时间 |
| STATUS | VARCHAR(11) | 统计信息收集操作的状态<br>\*   IN PROGRESS：正在运行<br>\*   COMPLETED：完成 <br>\*   FAILED：失败 |
| PRIORITY | INTEGER | 任务在统计信息父操作中所有目标对象的排名 |
| NOTES | VARCHAR(4000) | 统计信息收集操作的注释，例如统计信息选项值和状态为FAILED的操作的失败信息 |
