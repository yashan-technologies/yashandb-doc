This view is used to query the segment information that has been allocated on all master nodes.

|Field |Type |Description |
|:-----------------|-------------| ------------------------------------------------------------ |
| GROUP_ID         | NUMBER      | Group ID                                                     |
| GROUP_NODE_ID    | NUMBER      | Node ID within the group                                     |
| INST_ID          | NUMBER      | Instance ID                                                  |
| OWNER            | VARCHAR(64) | Username to which the segment belongs                        |
| TABLE_NAME       | VARCHAR(64) | Name of the table to which the segment belongs              |
| TABLESPACE_NAME  | VARCHAR(64) | Name of the tablespace where the segment is located         |
| SEGMENT_NAME     | VARCHAR(64) | Name of the segment                                          |
| PARTITION_NAME    | VARCHAR(64) | If it is a partition object, then it is the partition name, otherwise NULL |
| SEGMENT_TYPE     | VARCHAR(18) | Segment type<br>*   TABLE<br>*   INDEX<br>*   LOB<br>*   TABLE PARTITION<br>*   INDEX PARTITION<br>*   LOB PARTITION<br>*   TABLE SUBPARTITION<br>*   INDEX SUBPARTITION<br>*   LOB SUBPARTITION |
| BYTES            | BIGINT      | Number of bytes of the segment                               |
| BLOCKS           | BIGINT      | Number of blocks of the segment                              |
| EXTENTS          | BIGINT      | Number of extents of the segment                             |