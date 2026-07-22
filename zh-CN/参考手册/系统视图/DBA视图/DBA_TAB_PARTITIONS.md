本视图显示所有分区表的分区信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_OWNER | VARCHAR(64) | 表的用户名 |
| TABLE\_NAME | VARCHAR(64) | 分区所属表名 |
| COMPOSITE | VARCHAR(1) | 是否为组合分区 |
| PARTITION\_NAME | VARCHAR(64) | 分区名 |
| SUBPARTITION\_COUNT | BIGINT | 当前分区的子分区个数 |
| HIGH\_VALUE | VARCHAR(4000) | 分区边界值字符串 |
| HIGH\_VALUE\_LENGTH | INTEGER | 分区边界值字符串的长度（单位：字节） |
| PARTITION\_POSITION | BIGINT | 分区在表内的位置 |
| TABLESPACE\_NAME | VARCHAR(64) | 分区所在表空间名称 |
| PCT\_FREE | INTEGER | 数据页面内至少预留空间的百分比 |
| INI\_TRANS | INTEGER | 数据页面初始化时预留的Xslot数量 |
| MAX\_TRANS | INTEGER | 数据页面最大可扩展到的Xslot的数量 |
| LOGGING | CHAR(1) | 分区是否记录redo<br>\*   Y<br>\*   N |
| NUM\_ROWS | BIGINT | 分区的行数 |
| BLOCKS | BIGINT | 分区的block数量 |
| EMPTY\_BLOCKS | BIGINT | 分区的空block数量 |
| AVG\_SPACE | INTEGER | 分区block的平均空闲空间（单位：字节） |
| CHAIN\_CNT | BIGINT | 分区的行链接数量 |
| AVG\_ROW\_LEN | INTEGER | 分区的平均行长度（单位：字节） |
| SAMPLE\_SIZE | BIGINT | 统计信息的采样行数 |
| LAST\_ANALYZED | DATE | 上次统计信息收集的时间 |
| INTERVAL$ | VARCHAR(1) | 是否INTERVAL分区<br>\*   Y<br>\*   N |
| SEGMENT\_CREATED | VARCHAR(1) | segment是否创建<br>\*   Y<br>\*   N    |
