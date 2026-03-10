本视图显示所有组合分区索引的每个二级分区信息。

|  字段| 类型| 说明|
| --------------------------- | ------------- | ------------------------------------------------ |
| INDEX_OWNER                | VARCHAR(64)   | 索引的用户名                                     |
| INDEX_NAME                 | VARCHAR(64)   | 索引名                                           |
| PARTITION_NAME             | VARCHAR(64)   | 索引一级分区名                                   |
| SUBPARTITION_NAME          | VARCHAR(64)   | 索引二级分区名                                   |
| HIGH_VALUE                 | VARCHAR(4000) | 索引二级分区边界值字符串                         |
| HIGH_VALUE_LENGTH         | INTEGER       | 索引二级分区边界值字符串的长度                   |
| PARTITION_POSITION         | BIGINT        | 索引分区在索引内的位置                           |
| SUBPARTITION_POSITION      | BIGINT        | 索引二级分区在一级分区内的位置                   |
| STATUS                      | VARCHAR(8)    | 索引分区是否可用<br>\*   USABLE<br>\*   UNUSABLE |
| TABLESPACE_NAME            | VARCHAR(64)   | 索引二级分区所在表空间的名称                     |
| PCT_FREE                   | INTEGER       | 数据页面内至少预留空间的百分比                   |
| INI_TRANS                  | INTEGER       | 数据页面初始化时预设的Xslot的数量                |
| MAX_TRANS                  | INTEGER       | 数据页面最大可扩展到的Xslot的数量                |
| LOGGING                     | CHAR(1)       | 索引分区是否记录redo<br>\*   Y<br>\*   N         |
| BLEVEL                      | INTEGER       | BTree的层数                                      |
| LEAF_BLOCKS                | BIGINT        | BTree的叶子节点的个数                            |
| DISTINCT_KEYS              | BIGINT        | 唯一键值的个数                                   |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT        | 每个键值占用的平均叶子block数量                  |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT        | 每个键值占用的平均block数量                      |
| NUM_ROWS                   | BIGINT        | 索引的行数                                       |
| SAMPLE_SIZE                | BIGINT        | 索引分区统计信息的采样行数                       |
| LAST_ANALYZED              | DATE          | 上一次执行统计信息收集的时间                     |
| INTERVAL$                   | VARCHAR(1)    | 是否INTERVAL分区<br>\*   Y<br>\*   N             |
| SEGMENT_CREATED            | VARCHAR(1)    | segment是否创建<br>\*   Y<br>\*   N              |
