本视图显示所有的索引信息。

| 字段  | 类型          | 说明  |
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 索引所属的用户名 |
| INDEX_NAME | VARCHAR(64) | 索引名称 |
| INDEX_TYPE | VARCHAR(25) | 索引类型<br>*   NORMAL：BTree索引<br>*   NORMAL/REV：BTree Reverse索引<br>*   FUNCTION-BASED NORMAL：Function BTree 索引<br>*   FUNCTION-BASED NORMAL/REV：Function BTree Reverse索引<br>*   LOB：LOB索引<br>*   COLUMNAR：列式索引<br>*   RTREE：RTree索引|
| TABLE_OWNER | VARCHAR(64) | 表的用户名 |
| TABLE_NAME | VARCHAR(64) | 表名  |
| TABLE_TYPE | VARCHAR(11) | 表的类型<br>\*   TABLE |
| UNIQUENESS | VARCHAR(1)  | 是否是唯一索引<br>\*   Y：唯一索引<br>\*   N：非唯一索引 |
| COMPRESSION | CHAR(8)     | 是否压缩<br>\* Disable |
| PREFIX_LENGTH | INTEGER     | 压缩前缀的长度 |
| TABLESPACE_NAME | VARCHAR(64) | 索引所在表空间的名称 |
| INI_TRANS | INTEGER     | block初始事务个数 |
| MAX_TRANS | INTEGER     | block最大事务个数 |
| PCT_FREE | INTEGER     | block上预留的空间百分比 |
| LOGGING | CHAR(1)     | 是否记录redo日志<br>\*   Y：记录redo<br>\*   N：不记录redo |
| BLEVEL | INTEGER     | BTree的高度 |
| LEAF_BLOCKS | BIGINT      | 叶子block的数量 |
| DISTINCT_KEYS | BIGINT      | 唯一键值的数量 |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT      | 每个键值占用的平均叶子block数量 |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT      | 每个键值占用的平均block数量 |
| STATUS | VARCHAR(8)  | 状态<br>\*   UNUSABLE： 不可用<br>\*   VALID：可用<br>*   N/A：分区索引，在DBA_IND_PARTITIONS查看状态 |
| NUM_ROWS | BIGINT      | 索引的行数 |
| SAMPLE_SIZE | BIGINT      | 统计信息采样的行数 |
| LAST_ANALYZED | DATE        | 上次统计信息收集的时间 |
| PARTITIONED | VARCHAR(1)  | 是否为分区索引<br>\*   Y：是分区索引<br>\*   N：不是分区索引 |
| TEMPORARY | VARCHAR(1)  | 是否为临时索引<br>\*   Y：是临时索引<br>\*   N：不是临时索引 |
| GENERATED | VARCHAR(1)  | 是否为系统自动创建的索引名<br>\*   Y：系统自动创建的索引名<br>\*   N：用户创建的索引名 |
| VISIBILITY | VARCHAR(9)  | 索引的可见性<br>\*   VISIBLE：对SQL执行器可见<br>\*   INVISIBLE：对SQL执行器不可见 |
| DATABASE_MAINTAINED | VARCHAR(1)  | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的索引<br>\*   N：用户创建的索引 |
| CONSTRAINT_INDEX | VARCHAR(1)  | 是否为约束自动创建的索引<br/>\*   Y：约束自动创建的索引名<br/>\*   N：用户创建的索引 |
| ORPHANED_ENTRIES | VARCHAR(1) | 全局索引是否包含孤儿键值对<br/>\*   Y：不包含<br/>\*   N：包含 |



