本视图显示上次统计信息收集之后，发生变化的表信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE_OWNER | VARCHAR(64) | 该表的用户名 |
| TABLE\_NAME | VARCHAR(64) | 表名  |
| PARTITION_NAME | VARCHAR(64) | 分区名 |
| SUBPARTITION_NAME | VARCHAR(1) | 二级分区名 |
| INSERTS | BIGINT | 插入的行数 |
| UPDATES | BIGINT | 更新的行数 |
| DELETES | BIGINT | 删除的行数 |
| TIMESTAMP | DATE | 上次变更的时间 |
| TRUNCATED | VARCHAR(1) | 是否被truncate |
| DROP_SEGMENTS | BIGINT | segment被drop的次数 |
