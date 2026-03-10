This view displays the information of all allocated segments.

|Field |Type |Description |
| ---------------- | ----------- | ------------------------------------------------------------ |
| OWNER            | VARCHAR(64) | The username to which the segment belongs                   |
| SEGMENT_NAME    | VARCHAR(64) | The name of the segment                                      |
| PARTITION_NAME  | VARCHAR(64) | If it is a partition object, it will be the partition name; otherwise, it is NULL |
| SEGMENT_TYPE    | VARCHAR(18) | Segment type<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>\*   LOB PARTITION<br>\*  AC PARTITION<br>\*  TABLE SUBPARTITION <br>\*  INDEX SUBPARTITION<br>\*  LOB SUBPARTITION |
| TABLESPACE_NAME | VARCHAR(64) | The tablespace name where the extent is located             |
| EXTENT_ID        | INTEGER     | The ID of the extent within the segment                      |
| FILE_ID          | INTEGER     | The file ID where the extent is located                     |
| BLOCK_ID         | INTEGER     | The block ID of the extent                                   |
| BYTES            | BIGINT      | The number of bytes of the extent                            |
| BLOCKS           | INTEGER     | The number of blocks in the extent                           |