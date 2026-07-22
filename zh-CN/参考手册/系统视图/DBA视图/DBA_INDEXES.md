本视图显示所有的索引信息。

|  字段| 类型| 说明|
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 索引所属的用户名 |
| INDEX\_NAME | VARCHAR(64) | 索引名称 |
| INDEX\_TYPE | VARCHAR(25) | 索引类型<br>*   NORMAL：BTree索引<br>*   NORMAL/REV：BTree Reverse索引<br>*   FUNCTION-BASED NORMAL：Function BTree 索引<br>*   FUNCTION-BASED NORMAL/REV：Function BTree Reverse索引<br>*   LOB：Lob索引<br>*   COLUMNAR：列式索引<br>*   RTREE：RTree索引<br>* BITMAP：BITMAP索引<br>*   VECTOR：向量索引<br>*   SEARCH：全文索引|
| TABLE\_OWNER | VARCHAR(64) | 表的用户名 |
| TABLE\_NAME | VARCHAR(64) | 表名  |
| TABLE\_TYPE | VARCHAR(11) | 表的类型<br>\*   TABLE |
| UNIQUENESS | VARCHAR(1)  | 是否是唯一索引<br>\*   Y：唯一索引<br>\*   N：非唯一索引 |
| COMPRESSION | CHAR(8)     | 是否压缩<br>\* Disable |
| PREFIX\_LENGTH | INTEGER     | 压缩前缀的长度 |
| TABLESPACE\_NAME | VARCHAR(64) | 索引所在表空间的名称 |
| INI\_TRANS | INTEGER     | block初始事务个数 |
| MAX\_TRANS | INTEGER     | block最大事务个数 |
| PCT\_FREE | INTEGER     | block上预留的空间百分比 |
| LOGGING | CHAR(1)     | 是否记录redo日志<br>\*   Y：记录redo<br>\*   N：不记录redo |
| BLEVEL | INTEGER     | BTree的高度<br/>仅在收集完对象的统计信息后才有值 |
| LEAF\_BLOCKS | BIGINT      | 叶子block的数量<br/>仅在收集完对象的统计信息后才有值 |
| DISTINCT\_KEYS | BIGINT      | 唯一键值的数量<br/>仅在收集完对象的统计信息后才有值 |
| AVG\_LEAF\_BLOCKS\_PER\_KEY | BIGINT      | 每个键值占用的平均叶子block数量<br/>仅在收集完对象的统计信息后才有值 |
| AVG\_DATA\_BLOCKS\_PER\_KEY | BIGINT      | 每个键值占用的平均block数量<br/>仅在收集完对象的统计信息后才有值 |
| STATUS | VARCHAR(8)  | 状态<br>\*   UNUSABLE： 不可用<br>\*   VALID：可用<br>*   N/A：分区索引，在DBA_IND_PARTITIONS查看状态 |
| NUM\_ROWS | BIGINT      | 索引的行数<br/>仅在收集完对象的统计信息后才有值 |
| SAMPLE\_SIZE | BIGINT      | 统计信息采样的行数<br/>仅在收集完对象的统计信息后才有值 |
| LAST\_ANALYZED | DATE        | 上次统计信息收集的时间<br/>仅在收集完对象的统计信息后才有值 |
| PARTITIONED | VARCHAR(1)  | 是否为分区索引<br>\*   Y：是分区索引<br>\*   N：不是分区索引 |
| TEMPORARY | VARCHAR(1)  | 是否为临时索引<br>\*   Y：是临时索引<br>\*   N：不是临时索引 |
| GENERATED | VARCHAR(1)  | 是否为系统自动创建的索引名<br>\*   Y：系统自动创建的索引名<br>\*   N：用户创建的索引名 |
| GLOBAL\_STATS | VARCHAR(3)  | 是否为全局统计信息 |
| VISIBILITY | VARCHAR(9)  | 索引的可见性<br>\*   VISIBLE：对SQL执行器可见<br>\*   INVISIBLE：对SQL执行器不可见 |
| DATABASE\_MAINTAINED | VARCHAR(1)  | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的索引<br>\*   N：用户创建的索引 |
| CONSTRAINT_INDEX | VARCHAR(1)  | 是否为约束自动创建的索引<br/>\*   Y：约束自动创建的索引名<br/>\*   N：用户创建的索引 |
| ORPHANED_ENTRIES | VARCHAR(1)  | 全局索引是否包含孤儿键值对<br/>\*   Y：不包含<br/>\*   N：包含 |
| ANSI_NULL | VARCHAR(1)  | 索引对NULL的处理是否按照ANSI标准<br/>\*   Y：是<br/>\*   N：否 |
| NULL_FIRST | VARCHAR(1)  | 索引对NULL的处理是否NULL值排在最前面<br/>\*   Y：是<br/>\*   N：否 |
| FLASHBACK_SCN | BIGINT | 当前索引可用于闪回查询的SCN，若该值为NULL则视作FLASHBACK_SCN等于数据库启动时刻对应的SCN，创建索引（CREATE INDEX）、重建索引（ALTER INDEX REBUILD）以及设置索引不可用（ALTER INDEX UNUSABLE）操作会推进该值<br/>如果闪回查询指定的SCN大于对应表、表上的索引可用于闪回查询的SCN的最大值，则系统会自动基于对应索引进行闪回查询  |
