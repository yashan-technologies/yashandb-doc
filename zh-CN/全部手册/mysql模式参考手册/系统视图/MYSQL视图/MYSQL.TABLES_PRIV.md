本视图显示所有表级别的权限信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| HOST | VARCHAR(60) | 数据库实例所在服务器的名称 |
| DB | VARCHAR(64) | 数据库的名称 |
| USER | VARCHAR(64) | 数据库用户的用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| GRANTOR | VARCHAR(93) | 发起授权者用户名 |
| TIMESTAMP | TIMESTAMP | 时间戳 |
| TABLE_PRIV | VARCHAR(1500) | 表的权限 |
| COLUMN_PRIV | CHAR(0) | 列的权限 <br>YashanDB暂未开放列级别权限配置，该字段显示为空 |
