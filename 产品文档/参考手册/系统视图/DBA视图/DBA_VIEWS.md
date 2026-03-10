本视图显示所有视图的信息。

| 字段                   | 类型          | 说明                                                           |
|----------------------|-------------|--------------------------------------------------------------|
| OWNER                | VARCHAR(64) | 视图所属用户名                                                      |
| OWNERID              | INTEGER     | 视图所属用户ID                                                     |
| VIEW_NAME           | VARCHAR(64) | 视图名字                                                         |
| OBJECT_ID           | BIGINT      | 视图对象ID                                                       |
| DATABASE_MAINTAINED | VARCHAR(1)  | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的视图<br>\*   N：用户创建的视图 |
| TEXT_LENGTH         | INTEGER     | 视图定义语句长度                                                     |
| TEXT                 | CLOB        | 视图定义SQL语句                                                    |
| READ_ONLY           | VARCHAR(1)  | 视图是否只读                                                       |

