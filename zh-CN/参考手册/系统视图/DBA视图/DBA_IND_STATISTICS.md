本视图显示所有索引的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 索引所属用户名 |
| INDEX\_NAME | VARCHAR(64) | 索引名 |
| TABLE_OWNER | VARCHAR(64) | 被索引表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 被索引表名 |
| PARTITION\_NAME | VARCHAR(64) | 分区名 |
| PARTITION\_POSITION | BIGINT | 分区编号 |
| SUBPARTITION\_NAME | VARCHAR(1) | 子分区名|
| SUBPARTITION\_POSITION | VARCHAR(1) | 子分区编号 |
| OBJECT\_TYPE | VARCHAR(9) | 对象类型 |
| BLEVEL | INTEGER | BTree的高度 |
| LEAF\_BLOCKS| BIGINT | 叶子block的数量 |
| DISTINCT\_KEYS | BIGINT | 唯一值的数量 |
| DISTINCT\_FKEYS | BIGINT | 组合索引第一列的唯一值数量 |
| DISTINCT\_2KEYS | BIGINT | 组合索引前两列的唯一值数量 |
| DISTINCT\_3KEYS | BIGINT | 组合索引前三列的唯一值数量 |
| DISTINCT\_4KEYS | BIGINT | 组合索引前四列的唯一值数量 |
| AVG\_LEAF\_BLOCKS\_PER_KEY | BIGINT | 每个叶子block平均key的数量 |
| AVG\_DATA\_BLOCKS\_PER_KEY | BIGINT | 每个block平均key的数量 |
| CLUSTERING\_FACTOR | BIGINT | 索引聚集因子 |
| NUM\_ROWS| BIGINT | 索引key的数量 |
| SAMPLE\_SIZE | BIGINT | 采样的行数 |
| LAST\_ANALYZED | DATE | 最后一次收集的时间 |
| GLOBAL\_STATS | VARCHAR(1) | 是否为全局统计信息 |
| USER\_STATS | VARCHAR(1) | 是否为用户设置的统计信息 |
| LOCKED_STATS | VARCHAR(1) | 统计信息是否被锁定<br/>* Y：锁定<br/>* N：未锁定<br/>* 空：未收集统计信息 |
| STALE\_STATS | CHAR(1) | 统计信息是否已失效，当前版本共享集群/分布式集群部署模式下的失效判断只基于当前实例的修改<br/>\* Y：失效<br/>\* N：未失效<br/>\* 空：未收集统计信息 |
| SCOPE | CHAR(6) | 临时表索引统计信息的范围 |
