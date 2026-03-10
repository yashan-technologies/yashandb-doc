本视图显示所有LOB对象信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| OWNER | VARCHAR(64) | LOB对象所属的用户名 |
| TABLE_NAME | VARCHAR(64) | LOB对象所属的表名 |
| COLUMN_NAME | VARCHAR(4000) | LOB对象所属的列名 |
| SEGMENT_NAME | VARCHAR(64) | LOB对象的segment名 |
| TABLESPACE_NAME | VARCHAR(64) | LOB对象所属表空间的名称 |
| INDEX_NAME | VARCHAR(64) | LOB对象的索引名称 |
| IN_ROW | VARCHAR(1) | LOB数据是否是行内存储 |
| PARTITIONED | VARCHAR(1) | 是否是分区LOB |
| SEGMENT_CREATED | VARCHAR(1) | LOB对象是否存在segment |

