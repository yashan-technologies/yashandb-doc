本视图显示所有LOB对象的分区信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_OWNER | VARCHAR(64) | LOB对象所属的用户名 |
| TABLE\_NAME | VARCHAR(64) | LOB对象所属的表名 |
| COLUMN\_NAME | VARCHAR(64) | LOB对象所属的列名 |
| LOB\_NAME | VARCHAR(64) | LOB对象名 |
| PARTITION\_NAME | VARCHAR(64) | LOB分区所属表分区名 |
| LOB\_PARTITION\_NAME | VARCHAR(64) | LOB分区名 |
| LOB\_INDPART\_NAME | VARCHAR(64) | LOB分区对应LOB索引分区名 |
| PARTITION\_POSITION | BIGINT | LOB分区号 |
| COMPOSITE | VARCHAR(1) | LOB分区是否存在二级分区 |
| IN\_ROW | VARCHAR(1) | LOB数据是否是行内存储 |
| TABLESPACE\_NAME | VARCHAR(64) | LOB对象所属表空间的名称 |
| SEGMENT\_CREATED | VARCHAR(3) | LOB segment是否创建 |
