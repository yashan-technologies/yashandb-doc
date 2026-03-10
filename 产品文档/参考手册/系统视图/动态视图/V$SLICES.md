本视图显示所有LSC表冷数据的存储相关统计信息，以slice为单位进行展示。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| OWNER  | VARCHAR(64) | slice所属用户名 |
| TABLESPACE_NAME | VARCHAR(64) | slice所在的表空间名 |
| TABLE_NAME | VARCHAR(64) | slice所在的表名称 |
| PARTITION_NAME | VARCHAR(64) | slice所在的分区名，非分区表该字段为NULL  |
| SORTED | BOOLEAN | 是否排序 |
| COMPACTED | BOOLEAN | 是否合并 |
| FILE_ID  | BIGINT |	slice的unique ID |
| FILE_SIZE | BIGINT | slice文件大小，包含删除列，4K对齐|
| ROW_COUNT | BIGINT |slice存储行数|
