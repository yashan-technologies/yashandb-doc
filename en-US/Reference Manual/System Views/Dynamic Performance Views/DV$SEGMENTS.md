This view is used to query the segment information allocated to all master nodes in the ISC distributed cluster.

|Field |Type |Description |
| :--------------- | ----------- | ------------------------------------------------------------ |
| GROUP\_ID         | INTEGER     | Group ID                                                   |
| GROUP\_NODE\_ID   | INTEGER     | Node ID within the group                                   |
| OWNER             | VARCHAR(64) | User name of the segment owner                            |
| TABLE\_NAME       | VARCHAR(64) | Name of the table to which the segment belongs            |
| TABLESPACE\_NAME  | VARCHAR(64) | Name of the tablespace where the segment is located       |
| SEGMENT\_NAME     | VARCHAR(64) | Name of the segment                                       |
| PARTITION\_NAME   | VARCHAR(64) | If it is a partition object, it is the partition name; otherwise, it is NULL |
| SEGMENT\_TYPE     | VARCHAR(18) | Segment type<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   TABLE PARTITION<br>\*   INDEX PARTITION<br>\*   LOB PARTITION<br>\*   TABLE SUBPARTITION<br>\*   INDEX SUBPARTITION<br>\*   LOB SUBPARTITION |
| BYTES             | BIGINT      | Number of bytes of the segment                             |
| BLOCKS            | BIGINT      | Number of blocks of the segment                            |
| EXTENTS           | BIGINT      | Number of extents of the segment                           |