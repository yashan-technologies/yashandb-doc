本视图显示所有组合分区索引的每个二级分区信息。

|  字段| 类型| 说明|
| --------------------------- | ------------- | ------------------------------------------------ |
| INDEX\_OWNER                | VARCHAR(64)   | 索引的用户名                                     |
| INDEX\_NAME                 | VARCHAR(64)   | 索引名                                           |
| PARTITION\_NAME             | VARCHAR(64)   | 索引一级分区名                                   |
| SUBPARTITION\_NAME          | VARCHAR(64)   | 索引二级分区名                                   |
| HIGH\_VALUE                 | VARCHAR(4000) | 索引二级分区边界值字符串                         |
| HIGH\_VALUE\_LENGTH         | INTEGER       | 索引二级分区边界值字符串的长度                   |
| PARTITION\_POSITION         | BIGINT        | 索引分区在索引内的位置                           |
| SUBPARTITION\_POSITION      | BIGINT        | 索引二级分区在一级分区内的位置                   |
| STATUS                      | VARCHAR(8)    | 索引分区是否可用<br>\*   USABLE<br>\*   UNUSABLE |
| TABLESPACE\_NAME            | VARCHAR(64)   | 索引二级分区所在表空间的名称                     |
| PCT\_FREE                   | INTEGER       | 数据页面内至少预留空间的百分比                   |
| INI\_TRANS                  | INTEGER       | 数据页面初始化时预设的Xslot的数量                |
| MAX\_TRANS                  | INTEGER       | 数据页面最大可扩展到的Xslot的数量                |
| LOGGING                     | CHAR(1)       | 索引分区是否记录redo<br>\*   Y<br>\*   N         |
| BLEVEL                      | INTEGER       | BTree的层数                                      |
| LEAF\_BLOCKS                | BIGINT        | BTree的叶子节点的个数                            |
| DISTINCT\_KEYS              | BIGINT        | 唯一键值的个数                                   |
| AVG\_LEAF\_BLOCKS\_PER\_KEY | BIGINT        | 每个键值占用的平均叶子block数量                  |
| AVG\_DATA\_BLOCKS\_PER\_KEY | BIGINT        | 每个键值占用的平均block数量                      |
| NUM\_ROWS                   | BIGINT        | 索引的行数                                       |
| SAMPLE\_SIZE                | BIGINT        | 索引分区统计信息的采样行数                       |
| LAST\_ANALYZED              | DATE          | 上一次执行统计信息收集的时间                     |
| INTERVAL$                   | VARCHAR(1)    | 是否INTERVAL分区<br>\*   Y<br>\*   N             |
| SEGMENT\_CREATED            | VARCHAR(1)    | segment是否创建<br>\*   Y<br>\*   N              |
