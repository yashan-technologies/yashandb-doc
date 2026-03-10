本视图显示所有的OUTLINE信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| NAME | VARCHAR(64) | 对象名称 |
| OWNER | VARCHAR(64) | 对象所属用户名 |
| CATEGORY | VARCHAR(64) | 类别名称 |
| USED | VARCHAR(6) | 是否被使用 |
| TIMESTAMP | TIMESTAMP | 对象创建时间 |
| VERSION | VARCHAR(64) | 数据库当前版本信息 |
| SQL_TEXT | CLOB | SQL_TEXT语法OUTLINE对应的SQL语句|
| COMPATIBLE | VARCHAR(12) | OUTLINE在迁移过程中是否兼容 |
| ENABLED | VARCHAR(8) | 是否使能 |
| FORMAT | VARCHAR(6) | hint格式 |
| MIGRATED | VARCHAR(12) | OUTLINE是否被迁移 |
| SQL_ID  | VARCHAR(13) | SQL_ID语法OUTLINE对应的SQL语句ID值|
