本视图描述数据库中所有二级分区对象的分区键列信息。

| 字段  | 类型          | 说明                               |
| --- |-------------|----------------------------------|
| OWNER | VARCHAR(64) | 二级分区对象的所有者                       |
| NAME | VARCHAR(64) | 二级分区对象的名称                        |
| OBJECT_TYPE | CHAR(5)     | 对象类型<br>\*   TABLE<br>\*   INDEX |
| COLUMN_NAME | VARCHAR(64) | 列名                               |
| COLUMN_POSITION | INTEGER     | 列在分区键中的位置                        |
| COLLATED_COLUMN_ID | INTEGER  | 内部使用的COLUMN ID                   |