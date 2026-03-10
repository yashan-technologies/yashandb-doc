本视图显示所有分区列的直方图信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| PARTITION_NAME | VARCHAR(64) | 分区名 |
| COLUMN_NAME | VARCHAR(64) | 列名 |
| ENDPOINT_NUMBER | BIGINT | endpoint编号 |
| ENDPOINT_VALUE | VARCHAR(1000) | endpoint值 |
| ENDPOINT_VALUE_RAW | RAW(1000) | endpoint二进制值 |
| ENDPOINT_REPEAT_COUNT | BIGINT | endpoint数量 |
| SCOPE | CHAR(6) | 保留字段，只针对全局临时表<br>\* SHARED：统计信息对全局有效<br>\* SESSION：统计信息只对会话有效 |
