本视图显示所有列的可操作信息。

|  字段| 类型| 说明|
|-----------------------|-------------|--------------------------------------------------------|
| OWNER                 | VARCHAR(64) | 表列所属用户名                                                |
| TABLE_NAME            | VARCHAR(64) | 列所属表名或视图名                                              |
| COLUMN_NAME           | VARCHAR(64) | 列名                                                     |
| UPDATABLE             | VARCHAR(3)  | 该列是否可被更新                                                 |
| INSERTABLE            | VARCHAR(3)  | 该列是否可被插入 |
| DELETABLE             | VARCHAR(3)  | 该列是否可被删除                                              |
