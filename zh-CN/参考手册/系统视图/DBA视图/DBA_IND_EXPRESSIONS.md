本视图显示所有的函数索引表达式信息。

|  字段| 类型| 说明|
|-------------------|---------------|----------------------------------|
| INDEX_OWNER       | VARCHAR(64)   | 索引的用户名                           |
| INDEX_NAME        | VARCHAR(64)   | 索引名                              |
| TABLE_OWNER       | VARCHAR(64)   | 表的用户名                            |
| TABLE_NAME        | VARCHAR(64)   | 表名                               |
| COLUMN_EXPRESSION | VARCHAR(8000) | 函数索引表达式                          |
| COLUMN_POSITION   | INTEGER       | 列在索引中的位置                         |
