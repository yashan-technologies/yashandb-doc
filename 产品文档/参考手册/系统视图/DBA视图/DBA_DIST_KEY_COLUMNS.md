从v23.2.1起，本视图已弃用。原用于显示所有的分布键信息。

| 字段  | 类型            | 说明  |
| --- |---------------| --- |
| OWNER | VARCHAR(64)   | 分布对象的所有者 |
| NAME | VARCHAR(64)   | 分布对象的名称 |
| COLUMN_NAME | VARCHAR(64) | 列名               |
| COLUMN_POSITION | INTEGER | 列在分布键中的位置                                        |