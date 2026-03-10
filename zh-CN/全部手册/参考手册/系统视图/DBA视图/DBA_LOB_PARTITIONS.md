本视图显示所有LOB对象的分区信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE_OWNER | VARCHAR(64) | LOB对象所属的用户名 |
| TABLE_NAME | VARCHAR(64) | LOB对象所属的表名 |
| COLUMN_NAME | VARCHAR(64) | LOB对象所属的列名 |
| LOB_NAME | VARCHAR(64) | LOB对象名 |
| PARTITION_NAME | VARCHAR(64) | LOB分区所属表分区名 |
| LOB_PARTITION_NAME | VARCHAR(64) | LOB分区名 |
| LOB_INDPART_NAME | VARCHAR(64) | LOB分区对应LOB索引分区名 |
| PARTITION_POSITION | BIGINT | LOB分区号 |
| COMPOSITE | VARCHAR(1) | LOB分区是否存在二级分区 |
| IN_ROW | VARCHAR(1) | LOB数据是否是行内存储 |
| TABLESPACE_NAME | VARCHAR(64) | LOB对象所属表空间的名称 |
| SEGMENT_CREATED | VARCHAR(3) | LOB segment是否创建 |
