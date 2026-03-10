本视图显示数据库所有同义词信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 同义词所属的用户名 |
| SYNONYM_NAME | VARCHAR(64) | 同义词名称 |
| TABLE_OWNER | VARCHAR(64) | 被引用对象所属的用户名 |
| TABLE_NAME | VARCHAR(64) | 被引用对象的名称 |
| DB_LINK | VARCHAR(64) | 引用database link的名称 |
| DATABASE_MAINTAINED | VARCHAR(1) | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的同义词<br>\*   N：用户创建的同义词 |
