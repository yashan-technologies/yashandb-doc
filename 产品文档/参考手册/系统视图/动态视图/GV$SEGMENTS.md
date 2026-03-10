本视图用于查询所有主节点上已经分配的segment信息。

| 字段               | 类型          | 说明                                                         |
|:-----------------|-------------| ------------------------------------------------------------ |
| GROUP_ID         | NUMBER      | 组ID |
| GROUP_NODE_ID    | NUMBER      | 组内节点ID |
| INST_ID         | NUMBER      | 实例ID |
| OWNER            | VARCHAR(64) | segment所属的用户名                                          |
| TABLE_NAME       | VARCHAR(64) | segment所属表的名称                                          |
| TABLESPACE_NAME | VARCHAR(64) | segment所在的表空间名                                        |
| SEGMENT_NAME    | VARCHAR(64) | segment名称                                                  |
| PARTITION_NAME  | VARCHAR(64) | 如果是分区对象，则为分区名，否则为NULL                       |
| SEGMENT_TYPE    | VARCHAR(18) | segment类型<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   TABLE PARTITION<br>\*   INDEX PARTITION<br>*   LOB PARTITON<br>*   TABLE SUBPARTITION<br>*   INDEX SUBPARTITION<br>*   LOB SUBPARTITION |
| BYTES            | BIGINT      | segment的字节数                                              |
| BLOCKS           | BIGINT      | segment的block数量                                           |
| EXTENTS          | BIGINT      | segment的extent数量                                          |