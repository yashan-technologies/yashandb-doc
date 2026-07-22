本视图显示字典缓存上索引的统计信息内容。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| OBJ# | BIGINT | 索引对象ID |
| PART# | BIGINT | 索引分区ID，若索引对象没有分区，则该字段为NULL |
| FLAGS | INTEGER | 索引的属性<br>\*   0X01：是否分区<br>\*   0X02：是否可用<br>\*   0X4：是否函数索引<br>\*   0X8：是否逆序<br>\*   0X10：是否被用作约束<br>\*   0X20：是否可见<br>\*   0X40：KEY上是否记录DATAOID<br>\*   0X80：标识statistics是否是由用户直接指定<br>\*   0X100：标识分区索引的statistics是全量统计得到还是由分区估算得到|
| ROW\_COUNT | BIGINT | 索引的行数 |
| LEAFBLOCK\_COUNT | BIGINT | Leaf Block数量 |
| DISTINCT\_KEYS | BIGINT | 唯一键值的个数 |
| DISTINCT\_FKEYS | BIGINT | 组合索引第一列唯一值的个数 |
| BTREE\_LEVEL | INTEGER | BTree的层数 |
| CLUSTER\_FACTOR | BIGINT | 集群因子 |
| AVG\_KEY\_LBLOCKS | BIGINT | 每个键值占用的平均叶子块数量 |
| AVG\_KEY\_DBLOCKS | BIGINT | 每个键值占用的平均数据块数量 |
| ANALYZE\_TIME | DATE | 上次收集统计信息的时间 |
| SAMPLESIZE | BIGINT | 统计信息采样的行数 |
| DISTINCT\_2KEYS | BIGINT | 组合索引前两列的唯一值数量 |
| DISTINCT\_3KEYS | BIGINT | 组合索引前三列的唯一值数量 |
| DISTINCT\_4KEYS | BIGINT | 组合索引前四列的唯一值数量 |
