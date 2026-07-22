本视图显示所有授权的对象权限信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GRANTEE | VARCHAR(64) | 被授权者名 |
| OWNER | VARCHAR(64) | 对象所属用户名 |
| TABLE\_NAME | VARCHAR(64) | 表名  |
| TYPE | VARCHAR(9) | 对象类型<br>\*   TABLE：表 <br>\*   VIEW：视图 <br>\*   SEQUENCE：序列 <br>\*   TYPE：自定义数据类型<br>\*   PROCEDURE：存储过程 <br>\*   UDF：自定义函数 <br>\*   PACKAGE：自定义高级包 |
| GRANTOR | VARCHAR(64) | 发起授权者用户名 |
| PRIVILEGE | VARCHAR(64) | 所授对象权限名 |
| GRANTABLE | VARCHAR(1) | 是否可为其他用户授权<br>\*   Y<br>\*   N |
