本视图显示已分配的segment中的extent信息。

|  字段| 类型| 说明|
| ---------------- | ----------- | ------------------------------------------------------------ |
| OWNER            | VARCHAR(64) | segment所属的用户名                                          |
| SEGMENT\_NAME    | VARCHAR(64) | segment名称                                                  |
| PARTITION\_NAME  | VARCHAR(64) | 如果是分区对象，则为分区名，否则为NULL                       |
| SEGMENT\_TYPE    | VARCHAR(18) | segment类型<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>*   LOB PARTITION<br>\*  AC PARTITION<br>*  TABLE SUBPARTITION <br>*  INDEX SUBPARTITION<br>*  LOB SUBPARTITION |
| TABLESPACE\_NAME | VARCHAR(64) | extent所在的表空间名                                         |
| EXTENT_ID        | INTEGER     | extent在segment内的ID                                        |
| FILE_ID          | INTEGER     | extent所在的文件ID                                           |
| BLOCK_ID         | INTEGER     | extent的block ID                                             |
| BYTES            | BIGINT      | extent的字节数                                               |
| BLOCKS           | INTEGER     | extent的block数量                                            |
