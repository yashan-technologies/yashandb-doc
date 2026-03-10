本视图显示针对对象的审计类型。

|  字段| 类型| 说明|
| --- | --- | --- |
| ACTION | INTEGER | 对象行为ID |
| NAME | VARCHAR(64) | 对象行为审计项名称：<br/>* ALL：全部，即包含后文所有对象行为审计项<br/>* SELECT：对目标对象执行查询操作<br/>* INSERT：向目标对象插入数据<br/>* UPDATE：更新目标对象<br/>* DELETE：删除目标对象的数据记录<br/>* ALTER：更改目标对象<br/>* COMMENT：在目标对象上添加注释<br/>* GRANT：向用户/角色授予目标对象相关的权限<br/>* INDEX：在目标对象上创建索引<br/>* LOCK：对目标对象加锁<br/>* EXECUTE：执行目标对象<br/>* FLASHBACK：闪回目标对象 |
