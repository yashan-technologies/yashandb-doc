This view displays information about all allocated segments.

|Field |Type |Description |
|------------------| --- | --- |
| OWNER              | VARCHAR(64) | The username to which the segment belongs                |
| SEGMENT\_NAME      | VARCHAR(64) | The name of the segment                                   |
| PARTITION\_NAME    | VARCHAR(64) | If it is a partition object, this is the partition name; otherwise, it is NULL |
| SEGMENT\_TYPE      | VARCHAR(18) | The type of the segment<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>\*  AC PARTITION<br>\*  TABLE SUBPARTITION<br>\*  INDEX SUBPARTITION<br>\*  LOB PARTITION<br>\*  LOB SUBPARTITION |
| SEGMENT\_SUBTYPE   | CHAR(4) | The subtype of the segment<br>\*   ASSM                  |
| TABLESPACE\_NAME   | VARCHAR(64) | The name of the tablespace where the segment is located  |
| HEADER\_FILE       | BIGINT     | The file ID where the segment block is located            |
| HEADER\_BLOCK      | NUMBER     | The block ID of the segment block                          |
| BYTES              | NUMBER | The size of the segment in bytes                          |
| BLOCKS             | BIGINT     | The number of blocks in the segment                        |
| EXTENTS            | BIGINT     | The number of extents in the segment                       |
| INITIAL\_EXTENT    | NUMBER     | The size of the first extent of the segment, in bytes     |
| NEXT\_EXTENT       | NUMBER     | Compatibility field, has no actual meaning, fixed the same as INITIAL\_EXTENT |
| MIN\_EXTENTS       | INTEGER    | Compatibility field, has no actual meaning, fixed to 1    |
| MAX\_EXTENTS       | BIGINT     | Compatibility field, has no actual meaning, fixed to 4294967295 |
| MAX\_SIZE          | BIGINT     | Compatibility field, has no actual meaning, fixed to 4294967295 |
| RETENTION          | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| MINRETENTION       | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| PCT\_INCREASE      | INTEGER    | Compatibility field, has no actual meaning, fixed to 0    |
| FREELISTS          | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| FREELIST\_GROUPS   | VARCHAR(1) | Compatibility field, has no actual meaning, fixed to NULL |
| RELATIVE\_FNO      | BIGINT     | The file ID where the segment block is located            |
| BUFFER\_POOL       | CHAR(7)   | Compatibility field, has no actual meaning, fixed to DEFAULT |