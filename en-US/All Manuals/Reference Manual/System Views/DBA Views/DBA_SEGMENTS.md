This view displays information about all allocated segments.

|Field |Type |Description |
|------------------| --- | --- |
| OWNER              | VARCHAR(64) | The username to which the segment belongs                |
| SEGMENT_NAME      | VARCHAR(64) | The name of the segment                                   |
| PARTITION_NAME    | VARCHAR(64) | If it is a partition object, this is the partition name; otherwise, it is NULL |
| SEGMENT_TYPE      | VARCHAR(15) | The type of the segment<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>\*  AC PARTITION<br>\*  TABLE SUBPARTITION<br>\*  INDEX SUBPARTITION<br>\*  LOB PARTITION<br>\*  LOB SUBPARTITION |
| SEGMENT_SUBTYPE   | VARCHAR(15) | The subtype of the segment<br>\*   ASSM                  |
| TABLESPACE_NAME   | VARCHAR(64) | The name of the tablespace where the segment is located  |
| HEADER_FILE       | BIGINT     | The file ID where the segment block is located            |
| HEADER_BLOCK      | NUMBER     | The block ID of the segment block                          |
| BYTES              | BIGINT     | The size of the segment in bytes                          |
| BLOCKS             | BIGINT     | The number of blocks in the segment                        |
| EXTENTS            | BIGINT     | The number of extents in the segment                       |
| INITIAL_EXTENT    | NUMBER     | The size of the first extent of the segment, in bytes     |
| NEXT_EXTENT       | NUMBER     | Compatibility field, has no actual meaning, fixed the same as INITIAL_EXTENT |
| MIN_EXTENTS       | INTEGER    | Compatibility field, has no actual meaning, fixed to 1    |
| MAX_EXTENTS       | BIGINT     | Compatibility field, has no actual meaning, fixed to 4294967295 |
| MAX_SIZE          | BIGINT     | Compatibility field, has no actual meaning, fixed to 4294967295 |
| RETENTION          | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| MINRETENTION       | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| PCT_INCREASE      | INTEGER    | Compatibility field, has no actual meaning, fixed to 0    |
| FREELISTS          | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| FREELIST_GROUPS   | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| RELATIVE_FNO      | BIGINT     | The file ID where the segment block is located            |
| BUFFER_POOL       | CHAR(7)   | Compatibility field, has no actual meaning, fixed to DEFAULT |