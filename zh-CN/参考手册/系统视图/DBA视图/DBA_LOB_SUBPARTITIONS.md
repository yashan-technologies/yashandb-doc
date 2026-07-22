本视图显示所有LOB对象的二级分区信息。

|  字段| 类型| 说明|
| ---------------------- | ----------- | -------------------------------- |
| TABLE\_OWNER           | VARCHAR(64) | LOB对象所属的用户名              |
| TABLE\_NAME            | VARCHAR(64) | LOB对象所属的表名                |
| COLUMN\_NAME           | VARCHAR(64) | LOB对象所属的列名                |
| LOB\_NAME              | VARCHAR(64) | LOB对象名                        |
| LOB\_PARTITION\_NAME   | VARCHAR(64) | LOB分区名                        |
| SUBPARTITION_NAME      | VARCHAR(64) | LOB二级分区所属二级分区名        |
| LOB_SUBPARTITION_NAME  | VARCHAR(64) | LOB二级分区名                    |
| LOB\_INDSUBPART\_NAME  | VARCHAR(64) | LOB二级分区对应LOB索引二级分区名 |
| SUBPARTITION\_POSITION | BIGINT      | LOB二级分区号                    |
| IN\_ROW                | VARCHAR(1)  | LOB数据是否是行内存储            |
| TABLESPACE\_NAME       | VARCHAR(64) | LOB对象所属表空间的名称          |
| SEGMENT\_CREATED       | VARCHAR(1)  | LOB segment是否创建              |
