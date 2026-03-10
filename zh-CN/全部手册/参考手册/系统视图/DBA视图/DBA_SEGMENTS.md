 本视图显示所有已经分配的segment信息。 

|  字段| 类型| 说明|
|------------------| --- | --- |
| OWNER            | VARCHAR(64) | segment所属的用户名 |
| SEGMENT_NAME    | VARCHAR(64) | segment名称 |
| PARTITION_NAME  | VARCHAR(64) | 如果是分区对象，则为分区名，否则为NULL |
| SEGMENT_TYPE    | VARCHAR(15) | segment类型<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>\*  AC PARTITION  <br>\*  TABLE SUBPARTITION  <br>\*  INDEX SUBPARTITION  <br>\*  LOB PARTITION  <br>\*  LOB SUBPARTITION  |
| SEGMENT_SUBTYPE | VARCHAR(15) | segment子类型<br>\*   ASSM  |
| TABLESPACE_NAME | VARCHAR(64) | segment所在的表空间名 |
| HEADER_FILE     | BIGINT | segment block所在的文件ID |
| HEADER_BLOCK    | NUMBER | segment block的block ID |
| BYTES            | BIGINT | segment的字节数 |
| BLOCKS           | BIGINT | segment的block数量 |
| EXTENTS          | BIGINT | segment的extent数量 |
| INITIAL_EXTENT  | NUMBER | segment的首个extent大小，单位为bytes|
| NEXT_EXTENT     | NUMBER | 兼容性字段，无实际含义，固定和INITIAL_EXTENT一样|
| MIN_EXTENTS     | INTEGER| 兼容性字段，无实际含义，固定为1|
| MAX_EXTENTS     | BIGINT | 兼容性字段，无实际含义，固定为4294967295|
| MAX_SIZE        | BIGINT | 兼容性字段，无实际含义，固定为4294967295|
| RETENTION        | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| MINRETENTION     | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| PCT_INCREASE    | INTEGER | 兼容性字段，无实际含义，固定为0|
| FREELISTS        | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| FREELIST_GROUPS | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| RELATIVE_FNO    | BIGINT| segment block所在的文件ID|
| BUFFER_POOL     | CHAR(7)| 兼容性字段，无实际含义，固定为DEFALULT|
