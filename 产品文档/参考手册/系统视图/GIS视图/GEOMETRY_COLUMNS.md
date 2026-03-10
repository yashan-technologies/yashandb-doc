本视图显示当前用户可访问的所有ST_GEOMETRY类型的列信息。

| 字段  | 类型          | 说明                                                                                  |
| --- |-------------|-------------------------------------------------------------------------------------|
| F_TABLE_CATALOG | VARCHAR(64)   | 该列所在表所属的数据库名                                              |
| F_TABLE_SCHEMA         | VARCHAR(64)   | 该列所在表的所有者用户名                                                    |
| F_TABLE_NAME            | VARCHAR(64)       | 该列所在的表名                                                |
| F_GEOMETRY_COLUMN          | VARCHAR(64)       | 列名                                      |
| COORD_DIMENSION         | NUMBER       | 列数据的维度                                      |
| SRID           | INTEGER        | 列数据的空间参考系标识号                                            |
| TYPE        | VARCHAR(19)   | 列数据的子类型，POINT等 |
