本视图显示所有表的身份列信息。

|  字段| 类型| 说明|
|----------------------------|---------------|---------------------------------------------------------|
| OWNER                      | VARCHAR(64)   | 该列所属对象的用户名称                                             |
| TABLE_NAME                | VARCHAR(64)   | 该列所属对象名称                                                |
| COLUMN_NAME               | VARCHAR(64)   | 列名                                                      |
| GENERATION_TYPE           | VARCHAR(10)   | 身份列的类型（BY DEFAULT或ALWAYS）                                                  |
| SEQUENCE_NAME             | VARCHAR(64)   | SEQUENCE名称 |
| IDENTITY_OPTIONS          | VARCHAR(318)  | 身份列对应SEQUENCE的信息 |
