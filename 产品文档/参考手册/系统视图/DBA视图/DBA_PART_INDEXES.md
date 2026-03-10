本视图显示所有分区索引信息。
This view displays all partition index information.

|  字段  /  Field | 类型  /  Type | 说明  /  Description |
| --- | --- | --- |
| OWNER | VARCHAR(64) | 分区索引所属的用户名 |
| OWNER | VARCHAR(64) | The username of the partition index owner |
| INDEX_NAME | VARCHAR(64) | 索引名 |
| INDEX_NAME | VARCHAR(64) | The name of the index |
| TABLE_NAME | VARCHAR(64) | 索引所属的表名 |
| TABLE_NAME | VARCHAR(64) | The name of the table to which the index belongs |
| PARTITIONING_TYPE | VARCHAR(5) | 分区类型<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| PARTITIONING_TYPE | VARCHAR(5) | The partition type<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(5) | 子分区类型<br/>\*   RANGE<br/>\*   HASH<br/>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(5) | The subpartition type<br/>\*   RANGE<br/>\*   HASH<br/>\*   LIST |
| PARTITION_COUNT | BIGINT | 分区数 |
| PARTITION_COUNT | BIGINT | The number of partitions |
| PARTITIONING_KEY_COUNT | INTEGER | 分区键数量 |
| PARTITIONING_KEY_COUNT | INTEGER | The number of partition keys |
| SUBPARTITIONING_KEY_COUNT | INTEGER | 子分区键数量 |
| SUBPARTITIONING_KEY_COUNT | INTEGER | The number of subpartition keys |
| LOCALITY | VARCHAR(6) | 分区索引类型<br>\* LOCAL<br>\* GLOBAL |
| LOCALITY | VARCHAR(6) | The partition index type<br>\* LOCAL<br>\* GLOBAL |
| DEF_TABLESPACE_NAME | VARCHAR(64) | 增加分区时的默认表空间名 |
| DEF_TABLESPACE_NAME | VARCHAR(64) | The default tablespace name when adding a partition |
| DEF_PCT_FREE | INTEGER | 增加分区时的默认PCTFREE，单位为百分比  |
| DEF_PCT_FREE | INTEGER | The default PCTFREE when adding a partition, in percentage |
| DEF_INI_TRANS | INTEGER | 增加分区时的默认INI_TRANS，单位为个数（页面初始化事务） |
| DEF_INI_TRANS | INTEGER | The default INI_TRANS when adding a partition, in number (initialization transactions per page) |
| DEF_MAX_TRANS | INTEGER | 增加分区时的默认MAX TRANS，单位为个数（页面允许的最大事务） |
| DEF_MAX_TRANS | INTEGER | The default MAX TRANS when adding a partition, in number (maximum transactions allowed per page) |
| DEF_LOGGING | CHAR(1) | 增加分区时的默认LOGGING属性，取值为Y或N |
| DEF_LOGGING | CHAR(1) | The default LOGGING attribute when adding a partition, with values of Y or N |