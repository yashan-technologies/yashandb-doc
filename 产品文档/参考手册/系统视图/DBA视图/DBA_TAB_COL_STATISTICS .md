本视图显示所有表的列统计信息。

| 字段  | 类型          | 说明  |
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| COLUMN_NAME | VARCHAR(64) | 列名 |
| NUM_DISTINCT | BIGINT      | 唯一值数量 |
| LOW_VALUE | RAW(1000)   | 最小值的二进制 |
| HIGH_VALUE | RAW(1000)   | 最大值的二进制  |
| DENSITY | FLOAT       | 列密度 |
| NUM_NULLS | BIGINT      | 空值数量 |
| NUM_BUCKETS | INTEGER     | 直方图的桶个数 |
| AVG_COL_LEN | INTEGER     | 平均列长度（单位：字节） |
| MAX_COL_LEN | INTEGER     | 最大列长度（单位：字节） |
| LAST_ANALYZED | DATE        | 最近一次收集统计信息的时间 |
| SAMPLE_SIZE | BIGINT      | 采样的记录数 |
| BLOCK_COUNT | BIGINT      | 占用块的数量 |
| GLOBAL_STATS | VARCHAR(1)  | 是否为全局统计信息 |
| USER_STATS | VARCHAR(1)  | 是否为用户设置的统计信息 |
| HISTOGRAM | VARCHAR(15) | 直方图的类型 |
| SCOPE | CHAR(6)     | 临时表统计信息的范围 |

