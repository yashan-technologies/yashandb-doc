This view shows information about all allocated segments.

|Field |Type |Description |
| :--------------- | ----------- | ------------------------------------------------------------ |
| OWNER            | VARCHAR(64) | Username of the segment owner                                |
| TABLE_NAME       | VARCHAR(64) | Name of the table to which the segment belongs              |
| TABLESPACE\_NAME | VARCHAR(64) | Name of the tablespace where the segment is located         |
| SEGMENT\_NAME    | VARCHAR(64) | Name of the segment                                          |
| PARTITION\_NAME  | VARCHAR(64) | If it is a partition object, it will be the partition name; otherwise, it is NULL |
| SEGMENT\_TYPE    | VARCHAR(18) | Segment type<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   TABLE PARTITION<br>\*   INDEX PARTITION<br>*   LOB PARTITION<br>*   TABLE SUBPARTITION<br>*   INDEX SUBPARTITION<br>*   LOB SUBPARTITION |
| BYTES            | BIGINT      | Size of the segment in bytes                                |
| BLOCKS           | BIGINT      | Number of blocks in the segment                              |
| EXTENTS          | BIGINT      | Number of extents in the segment                             |