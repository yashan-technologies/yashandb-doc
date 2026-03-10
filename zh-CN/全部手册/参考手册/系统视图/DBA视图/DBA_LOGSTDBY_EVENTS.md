本视图仅适用于逻辑备用数据库，用于记录在redo日志应用到逻辑备用数据库时所发生的故障及其相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
|EVENT_TIME  |TIMESTAMP  |记录事件的时间戳|
|START_SCN   |BIGINT     |事务在主数据库上启动的SCN。此SCN指的是主数据库上的SCN。|
|CURRENT_SCN |BIGINT     |当前已经回放的SCN，关联主库的SCN。如果发生故障，则检查此列以确定哪个存档日志文件包含故障源（例如：不受支持的记录）|
|COMMIT_SCN  |BIGINT     |在主数据库上提交更改的 SCN 值|
|XID         |INTEGER    |关联主库的事务ID|
|EVENT       |CLOB       |发生故障时正在处理的语句|
|STATUS_CODE |INTEGER    |YashanDB的错误码编号|
|STATUS      |VARCHAR(2048)|描述当前错误或者状态|
