本视图显示所有分区索引信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 分区索引所属的用户名 |
| INDEX_NAME | VARCHAR(64) | 索引名 |
| TABLE_NAME | VARCHAR(64) | 索引所属的表名 |
| PARTITIONING_TYPE | VARCHAR(5) | 分区类型<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(5) | 子分区类型<br/>\*   RANGE<br/>\*   HASH<br/>\*   LIST |
| PARTITION_COUNT | BIGINT | 分区数 |
| PARTITIONING_KEY_COUNT | INTEGER | 分区键数量 |
| SUBPARTITIONING_KEY_COUNT | INTEGER | 子分区键数量 |
| LOCALITY | VARCHAR(6) | 分区索引类型<br>\* LOCAL<br>\* GLOBAL |
| DEF_TABLESPACE_NAME | VARCHAR(64) | 增加分区时的默认表空间名 |
| DEF_PCT_FREE | INTEGER | 增加分区时的默认PCTFREE，单位为百分比  |
| DEF_INI_TRANS | INTEGER | 增加分区时的默认INI_TRANS，单位为个数（页面初始化事务） |
| DEF_MAX_TRANS | INTEGER | 增加分区时的默认MAX TRANS，单位为个数（页面允许的最大事务） |
| DEF_LOGGING | CHAR(1) | 增加分区时的默认LOGGING属性，取值为Y或N |
