本视图显示所有表的统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| OWNER | VARCHAR(64) | 表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| PARTITION_NAME | VARCHAR(64) | 分区名 |
| PARTITION_POSITION | BIGINT | 分区编号 |
| SUBPARTITION_NAME | VARCHAR(1) | 子分区名|
| SUBPARTITION_POSITION | VARCHAR(1) | 子分区编号 |
| OBJECT_TYPE | VARCHAR(9) | 对象类型 |
| NUM_ROWS | BIGINT | 行数 |
| BLOCKS | BIGINT | block数量 |
| EMPTY_BLOCKS | BIGINT | 空block数量 |
| AVG_SPACE | INTEGER | 每个block的平均空闲度（单位：字节） |
| CHAIN_CNT | BIGINT | 行链接的数量 |
| AVG_ROW_LEN | INTEGER | 平均的行长度（单位：字节） |
| SAMPLE_SIZE | BIGINT | 采样的行数 |
| LAST_ANALYZED | DATE | 最后一次收集的时间 |
| GLOBAL_STATS | VARCHAR(1) | 是否为全局统计信息 |
| USER_STATS | VARCHAR(1) | 是否为用户设置的统计信息 |
| LOCKED_STATS | VARCHAR(1) | 统计信息是否被锁定<br/>* Y：锁定<br/>* N：未锁定<br/>* 空：未收集统计信息 |
| STALE_STATS | CHAR(1) | 统计信息是否已失效，当前版本共享集群部署模式下的失效判断只基于当前实例的修改<br>\* Y：失效<br>\* N：未失效<br>\* 空：未收集统计信息 |
| SCOPE | CHAR(6) | 临时表统计信息的范围 |
