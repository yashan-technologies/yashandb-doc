本视图显示所有LOB对象信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | LOB对象所属的用户名 |
| TABLE\_NAME | VARCHAR(64) | LOB对象所属的表名 |
| COLUMN\_NAME | VARCHAR(4000) | LOB对象所属的列名 |
| SEGMENT\_NAME | VARCHAR(64) | LOB对象的segment名 |
| TABLESPACE\_NAME | VARCHAR(64) | LOB对象所属表空间的名称 |
| INDEX\_NAME | VARCHAR(64) | LOB对象的索引名称 |
| IN\_ROW | VARCHAR(1) | LOB数据是否是行内存储 |
| PARTITIONED | VARCHAR(1) | 是否是分区LOB |
| SEGMENT\_CREATED | VARCHAR(1) | LOB对象是否存在segment |
