本视图显示所有外部表的信息。

| 字段  | 类型          | 说明  |
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 外部表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 外部表名 |
| TYPE_OWNER | CHAR(3)     | 外部表驱动所属用户名 |
| TYPE_NAME | VARCHAR(12) | 外部表驱动名 |
| DEFAULT_DIRECTORY_OWNER | CHAR(3)     | 外部表默认目录所属用户名 |
| DEFAULT_DIRECTORY_NAME | VARCHAR(64) | 外部表默认目录名 |
| REJECT_LIMIT | VARCHAR(11) | 外部表错误容忍限制（可以忽略多少次数据错误）<br>* 数字 <br>* UNLIMITED |
| ACCESS_TYPE | VARCHAR(7)  | 访问参数类型: <br>* BLOB <br>* CLOB |
| ACCESS_PARAMETERS | CLOB        | 外部表的访问参数 |
| PROPERTY | VARCHAR(10) | 外部表属性: <br>* REFERENCED <br>* ALL |



