本视图显示所有的对象依赖情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 对象所属用户，该对象为依赖者 |
| NAME | VARCHAR(64) | 对象的名称，该对象为依赖者 |
| TYPE | VARCHAR(17) | 对象类型，该对象为依赖者 |
| REFERENCED_OWNER | VARCHAR(64) | 被依赖的对象所属用户 |
| REFERENCED_NAME | VARCHAR(64) | 被依赖的对象的名称 |
| REFERENCED_TYPE | VARCHAR(17) | 被依赖的对象类型 |
| REFERENCED_LINK_NAME | VARCHAR(1) | 预留字段 |
| DEPENDENCY_TYPE | VARCHAR(4) | 预留字段，值为HARD，表示对象强依赖 |
