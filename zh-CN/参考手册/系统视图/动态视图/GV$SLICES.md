本视图显示所有主节点上的LSC表冷数据的存储相关统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID      | NUMBER     | 组ID |
| GROUP_NODE_ID | NUMBER     | 组内节点ID |
| INST_ID       | NUMBER      | 实例ID|
| OWNER  | VARCHAR(64) | slice所属用户名 |
| TABLESPACE\_NAME | VARCHAR(64) | slice所在的表空间名 |
| TABLE\_NAME | VARCHAR(64) | slice所在的表名称 |
| PARTITION\_NAME | VARCHAR(64) | slice所在的分区名，非分区表该字段为NULL  |
| SORTED | BOOLEAN | 是否排序 |
| COMPACTED | BOOLEAN | 是否合并 |
| FILE\_ID  | BIGINT |	slice的unique ID |
| FILE\_SIZE | BIGINT | slice文件大小，包含删除列，4K对齐|
| ROW\_COUNT | BIGINT |slice存储行数|
