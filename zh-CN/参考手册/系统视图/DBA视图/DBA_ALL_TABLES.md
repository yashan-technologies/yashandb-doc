本视图显示所有的表，包括系统表的信息，和DBA_TABLES视图完全一致。

|  字段| 类型| 说明|
| --- |-------------|-------------------------------------------------------------------------------------|
|  OWNER | VARCHAR(64) | 该表的用户名                                                                              |
| TABLE\_NAME | VARCHAR(64) | 表名                                                                                  |
| TABLE_TYPE | VARCHAR(8) | 表的类型<br>*  HEAP表<br/>*  TAC表<br/>*  LSC表<br/>*  EXTERNAL表                          |
| TABLESPACE\_NAME | VARCHAR(64) | 该表所在的表空间名称                                                                          |
| STATUS | CHAR(5)     | 表状态<br>\*   VALID：可用                                                                |
| PCT\_FREE | INTEGER     | block上预留的空间百分比                                                                      |
| INI\_TRANS | INTEGER     | block初始事务个数                                                                         |
| MAX\_TRANS | INTEGER     | block最大事务个数                                                                         |
| LOGGING | VARCHAR(1)  | 是否记录redo日志<br>\*   Y：记录redo<br>\*   N：不记录redo                                       |
| CORRUPTED | CHAR(1)     | 是否是损坏的表<br/>\*   Y：表已损坏<br/>\*   N：表未损坏                                             |
| NUM\_ROWS | BIGINT      | 表的行数量                                                                               |
| BLOCKS | BIGINT      | 表的block数量                                                                           |
| EMPTY\_BLOCKS | BIGINT      | 表的空block数量                                                                          |
| AVG\_SPACE | INTEGER | 表block的平均空闲空间（单位：字节）<br/>仅在收集完对象的统计信息后才有值 |
| CHAIN\_CNT | BIGINT | 表的行链接数量<br/>仅在收集完对象的统计信息后才有值 |
| AVG\_ROW\_LEN | INTEGER | 表的平均行长度（单位：字节）<br/>仅在收集完对象的统计信息后才有值 |
| SAMPLE\_SIZE | BIGINT      | 统计信息采样的行数                                                                           |
| LAST\_ANALYZED | DATE        | 上次统计信息收集的时间                                                                         |
| COMPRESSION | VARCHAR(12) | 表采用的压缩方式，空表示不支持压缩，UNCOMPRESSED表示不压缩，其它对应指代压缩方式                                      |
| COMPRESSION\_LEVEL | VARCHAR(6)  | 表的压缩级别，空表示不支持，有LOW，MEDIUM和HIGH三种级别                                                  |
| MCOL\_TTL | BIGINT      | 表的可变列数据生命周期，单位：秒。超过此时间可变列数据有可能自动转换为不可变列数据                                           |
| PARTITIONED | VARCHAR(1)  | 是否为分区表<br>\*   Y：分区表<br>\*   N：不是分区表                                                |
| TEMPORARY | VARCHAR(1)  | 是否为临时表<br>\*   Y：临时表<br>\*   N：不是临时表                                                |
| ROW\_MOVEMENT | VARCHAR(7)  | 是否允许行移动<br>\*   ENABLE<br>\*   DISABLE                                              |
| TRANSFORM  | VARCHAR(7)  | LSC表是否允许可变数据转换为不可变数据 <br>\*   ENABLE<br>\*   DISABLE                                |
| COMPACT  | VARCHAR(7)  | LSC表是否允许不可变数据进行合并 <br>\*   ENABLE<br>\*   DISABLE                                |
| BUILD\_AC  | VARCHAR(7)  | LSC表是否允许不可变数据生成ac slice文件 <br>\*   ENABLE<br>\*   DISABLE                                |
| MCOL  | VARCHAR(7)  | LSC表是否开启MCOL能力 <br>\*   ENABLE<br>\*   DISABLE                                |
| DATABASE\_MAINTAINED | VARCHAR(1)  | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的表<br>\*   N ：用户创建的表                         |
| DURATION | VARCHAR(15) | 如果是临时表， duration为sys$session（会话级临时表） 或 sys$transaction（事务级临时表）， 表示临时表的持续时间， 否则为NULL |
| NESTED | VARCHAR(1) | 是否为嵌套表<br>\*   Y：嵌套表<br>\*   N：不是嵌套表 |
| SHARDED | VARCHAR(1) | 是否为分布表<br/>\*   Y：分布表<br/>\*   N：不是分布表 |
| DUPLICATED | VARCHAR(1) | 是否为复制表<br/>\*   Y：复制表<br/>\*   N：不是复制表 |
| SEGMENT_DEFERRED | VARCHAR(1) | 是否延迟创建SEGMENT<br/>\*  Y：延迟<br/>\*  N：不延迟 |
| DEGREE | INTEGER | 表的并行度 |
| INITIAL\_EXTENT  | NUMBER(29) | segment的首个extent大小，单位为bytes|
| NEXT\_EXTENT     | NUMBER(29) | 兼容性字段，无实际含义，固定和INITIAL_EXTENT一样|
| MIN\_EXTENTS     | INTEGER| 兼容性字段，无实际含义，固定为1|
| MAX\_EXTENTS     | BIGINT | 兼容性字段，无实际含义，固定为4294967295|
