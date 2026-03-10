本视图显示字典缓存上表的统计信息内容。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| OBJ# | BIGINT | 表对象ID |
| PART# | BIGINT | 表分区ID，若表对象没有分区，则该字段为NULL |
| FLAGS | INTEGER | 表的标识<br>\*   0X01：是否分布表<br>\*   0X02：是否复制表<br>\*   0X4：statistics是否由用户直接指定<br>\*   0X8：标识分区表的statistics是全量统计得到还是由分区估算得到 |
| ROW_COUNT | BIGINT | 表的行数 |
| BLOCK_COUNT | BIGINT | 表的数据块数量 |
| EMPTY_COUNT | BIGINT | 空数据块数量 |
| CHAIN_COUNT | BIGINT | 行链接数量 |
| AVG_ROW_SIZE | INTEGER | 平均行长度 |
| AVG_SPACE | INTEGER | 平均空间使用率 |
| ANALYZE_TIME | DATE | 上次收集统计信息的时间 |
| SAMPLESIZE | BIGINT | 统计信息采样的行数 |
