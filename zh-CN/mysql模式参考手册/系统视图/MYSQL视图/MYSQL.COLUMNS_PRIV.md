本视图显示列级别的权限信息。

YashanDB暂未开放列级别权限配置，该视图查询结果为空。

|  字段| 类型| 说明|
| --- | --- | --- |
| HOST | VARCHAR(1020) | 数据库实例所在服务器的名称 |
| DB | VARCHAR(64) | 数据库的名称 |
| USER | VARCHAR(64) | 数据库用户的用户名 |
| TABLE\_NAME | VARCHAR(64) | 表名 |
| COLUMN\_NAME | VARCHAR(64) | 列名 |
| TIMESTAMP | TIMESTAMP | 时间戳 |
| COLUMN\_PRIV | VARCHAR(1) | 列的权限 |
