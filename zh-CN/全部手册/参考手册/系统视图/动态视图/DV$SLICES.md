本视图显示存算一体分布式集群中所有主节点上的LSC表冷数据的存储相关统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| OWNER  | VARCHAR(64) | slice所属用户名 |
| TABLESPACE_NAME | VARCHAR(64) | slice所在的表空间名 |
| TABLE_NAME | VARCHAR(64) | slice所在的表名称 |
| PARTITION_NAME | VARCHAR(64) | slice所在的分区名，非分区表该字段为NULL  |
| SORTED | BOOLEAN | 是否排序 |
| COMPACTED | BOOLEAN | 是否合并 |
| FILE_ID  | BIGINT |	slice 的unique id |
| FILE_SIZE | BIGINT | slice文件大小，包含删除列，4K对齐|
| ROW_COUNT | BIGINT |slice存储行数|
