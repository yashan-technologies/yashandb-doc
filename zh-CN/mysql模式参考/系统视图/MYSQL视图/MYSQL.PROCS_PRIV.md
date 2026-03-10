本视图显示存储过程或函数的权限信息。

该视图仅完成兼容需求，查询结果为空。

|  字段| 类型| 说明|
| --- |----------------| --- |
| HOST | VARCHAR(60)       | 数据库实例所在服务器的名称 |
| DB | VARCHAR(64)       | 数据库的名称 |
| USER | VARCHAR(64)       | 数据库用户的用户名 |
| ROUTINE_NAME | VARCHAR(64)       | 存储过程或函数的名称 |
| ROUTINE_TYPE | VARCHAR(9)     | 存储过程或函数的类型 |
| GRANTOR | VARCHAR(93)       | 发起授权者用户名 |
| PROC_PRIV | LONGTEXT | 存储过程或函数的权限 |
| TIMESTAMP | TIMESTAMP      | 时间戳 |
