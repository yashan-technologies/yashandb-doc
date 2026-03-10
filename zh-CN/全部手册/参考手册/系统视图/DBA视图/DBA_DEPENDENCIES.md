本视图显示所有的对象依赖情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 对象用户名 |
| NAME | VARCHAR(64) | 对象名 |
| TYPE | VARCHAR(17) | 对象类型 |
| REFERENCED_OWNER | VARCHAR(64) | 依赖对象的用户名 |
| REFERENCED_NAME | VARCHAR(64) | 依赖对象名 |
| REFERENCED_TYPE | VARCHAR(17) | 依赖对象类型 |
| REFERENCED_LINK_NAME | VARCHAR(1) | 预留字段 |
| DEPENDENCY_TYPE | VARCHAR(4) | 预留字段，值为HARD，表示对象强依赖 |
