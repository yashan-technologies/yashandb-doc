本视图显示所有物化视图的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 物化视图所属用户名 |
| MVIEW\_NAME | VARCHAR(64) | 物化视图名字 |
| QUERY | CLOB | 物化视图创建的子查询语句 |
| QYERT\_LEN | INTEGER | 物化视图创建的子查询语句长度 |
| REFRESH\_MODE | VARCHAR(9) | 物化视图刷新模式 |
| REFRESH\_METHOD | VARCHAR(8) | 物化视图刷新类型 |
| REFRESH\_START\_DATE | VARCHAR(4000) | 物化视图首次刷新时间 |
| REFRESH\_NEXT\_DATE | VARCHAR(4000) | 物化视图刷新时间间隔 |
| DDL\_TIME | DATE | 物化视图发生DDL的时间戳 |
| REWRITE\_ENABLED | VARCHAR(1) | 是否允许查询重写<br>\*   Y<br>\*   N |
| BUILD\_MODE | VARCHAR(9) | 物化视图创建类型 |
| JOB | BIGINT | 物化视图JobId |
