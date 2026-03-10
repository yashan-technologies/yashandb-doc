本视图显示所有触发器中具有FOLLOWS或PRECEDES顺序的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TRIGGER_OWNER | VARCHAR(64) | 触发器所属用户名 |
| TRIGGER_NAME | VARCHAR(64) | 触发器名称 |
| REFERENCED_TRIGGER_OWNER | VARCHAR(128) | 触发器引用的触发器所属用户名 |
| REFERENCED_TRIGGER_NAME | VARCHAR(128) | 触发器引用的触发器的名称 |
| ORDERING_TYPE | VARCHAR(8) | 触发器与引用触发器之间的排序关系<br> * FOLLOWS |
