本视图显示数据库相关的信息。

| 字段                         | 类型          | 说明                    |
|----------------------------|-------------|-----------------------|
| CATALOG_NAME               | VARCHAR(3)     | schema所属目录名称，此值始终为def |
| SCHEMA_NAME                | VARCHAR(64) | schema的名称             |
| DEFAULT_CHARACTER_SET_NAME | VARCHAR(32) | schema的默认字符集名称        |
| DEFAULT_COLLATION_NAME     | VARCHAR(32) | schema的默认排序名称         |
| SQL_PATH                   | VARCHAR(1)  | 此值始终为NULL             |