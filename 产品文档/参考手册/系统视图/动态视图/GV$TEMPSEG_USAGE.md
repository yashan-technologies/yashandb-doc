本视图主要显示临时属性的segment信息。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| USERNAME  | VARCHAR(64)     | 会话关联用户名 |
| USER          | VARCHAR(64) | 会话关联用户名，与USERNAME列等效 |
| SID          | SMALLINT   | 会话ID |
| SESSION_NUM  | INTEGER   | 分配序列号 |
| SQL_ID  | VARCHAR(13)   | 会话正在执行的SQL ID（SQL文本的哈希/加密运算结果） |
| SQLHASH | BIGINT   | 会话正在执行的SQL哈希值（SQL文本计算得到） |
| TABLESPACE          | VARCHAR(64)   | 所占用的表空间名称 |
| CONTENTS          | VARCHAR(9)   | 表空间是否为临时属性。目前恒定为TEMPORARY |
| SEGTYPE          | VARCHAR(18)   | 空间占用者类型 <br/> * SORT：排序计算物化区 <br/> * HASH：哈希连接物化区 <br/> * MERGE_SORT：归并排序物化区 <br/> * QUEUE：队列物化区 <br/> * STACK：栈物化区 <br/> * LIST：列表物化区 <br/> * DATA：临时表数据 <br/> * INDEX：临时表索引 <br/> * LOB_DATA：临时LOB列数据 <br/> * LOB_INDEX：临时LOB列索引 |
| SEGFILE#          | INTEGER   | 所占用的数据文件ID号                                             |
| SEGBLK#          | INTEGER   | 若空间占用者是下列类型之一，该列值为占用空间的入口页面ID；否则该列值为空<br/> * DATA：临时表数据 <br/> * INDEX：临时表索引 <br/> * LOB_DATA：临时LOB列数据 <br/> * LOB_INDEX：临时LOB列索引 |
| EXTENTS          | BIGINT   | 所占用的extent数量 |
| BLOCKS          | BIGINT   | 所占用的block数量 |
| SEGRFNO#          | INTEGER   | 所占用的数据文件内部ID号 |
| TS#          | SMALLINT   | 所占用的表空间ID号 |