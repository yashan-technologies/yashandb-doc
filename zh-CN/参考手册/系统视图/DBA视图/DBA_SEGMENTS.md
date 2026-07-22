 本视图显示所有已经分配的segment信息。 

|  字段| 类型| 说明|
|------------------| --- | --- |
| OWNER            | VARCHAR(64) | segment所属的用户名 |
| SEGMENT\_NAME    | VARCHAR(64) | segment名称 |
| PARTITION\_NAME  | VARCHAR(64) | 如果是分区对象，则为分区名，否则为NULL |
| SEGMENT\_TYPE    | VARCHAR(18) | segment类型<br>\*   TABLE<br>\*   INDEX<br>\*   LOB<br>\*   AC<br>\*  TABLE PARTITION<br>\*   INDEX PARTITION<br>\*  AC PARTITION  <br>\*  TABLE SUBPARTITION  <br>\*  INDEX SUBPARTITION  <br>\*  LOB PARTITION  <br>\*  LOB SUBPARTITION  |
| SEGMENT\_SUBTYPE | CHAR(4) | segment子类型<br>\*   ASSM  |
| TABLESPACE\_NAME | VARCHAR(64) | segment所在的表空间名 |
| HEADER\_FILE     | BIGINT | segment block所在的文件ID |
| HEADER\_BLOCK    | NUMBER | segment block的block ID |
| BYTES            | NUMBER | segment的字节数 |
| BLOCKS           | BIGINT | segment的block数量 |
| EXTENTS          | BIGINT | segment的extent数量 |
| INITIAL\_EXTENT  | NUMBER | segment的首个extent大小，单位为bytes|
| NEXT\_EXTENT     | NUMBER | 兼容性字段，无实际含义，固定和INITIAL_EXTENT一样|
| MIN\_EXTENTS     | INTEGER| 兼容性字段，无实际含义，固定为1|
| MAX\_EXTENTS     | BIGINT | 兼容性字段，无实际含义，固定为4294967295|
| MAX\_SIZE        | BIGINT | 兼容性字段，无实际含义，固定为4294967295|
| RETENTION        | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| MINRETENTION     | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| PCT\_INCREASE    | INTEGER | 兼容性字段，无实际含义，固定为0|
| FREELISTS        | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| FREELIST\_GROUPS | VARCHAR(1)| 兼容性字段，无实际含义，固定为NULL|
| RELATIVE\_FNO    | BIGINT| segment block所在的文件ID|
| BUFFER\_POOL     | CHAR(7)| 兼容性字段，无实际含义，固定为DEFALULT|
