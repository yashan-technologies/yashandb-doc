本视图显示所有的分区表信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 分区表的用户名 |
| TABLE_NAME | VARCHAR(64) | 表名  |
| PARTITIONING_TYPE | VARCHAR(9) | 分区类型<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(9) | 子分区类型<br/>\*   NONE 非组合分区<br/>\*   RANGE<br/>\*   HASH<br/>\*   LIST |
| PARTITION_COUNT | BIGINT | 分区个数 |
| PARTITIONING_KEY_COUNT | INTEGER | 分区键包含的列数量 |
| SUBPARTITIONING_KEY_COUNT | INTEGER | 子分区键包含的列数量 |
| DEF_TABLESPACE_NAME | VARCHAR(64) | 分区的默认表空间名 |
| DEF_PCT_FREE | INTEGER | 分区的默认PCTFREE |
| DEF_INI_TRANS | INTEGER | 分区的默认INIT TRANS |
| DEF_MAX_TRANS | INTEGER | 分区的默认MAX TRANS  |
| DEF_LOGGING | CHAR(1) | 分区的默认LOGGING属性 |
| INTERVAL | VARCHAR(1000) | INTERVAL分区键值 |
