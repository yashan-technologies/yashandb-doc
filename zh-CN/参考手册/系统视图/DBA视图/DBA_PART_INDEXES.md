本视图显示所有分区索引信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 分区索引所属的用户名 |
| INDEX\_NAME | VARCHAR(64) | 索引名 |
| TABLE\_NAME | VARCHAR(64) | 索引所属的表名 |
| PARTITIONING\_TYPE | VARCHAR(5) | 分区类型<br>*   RANGE<br>*   HASH<br>*   LIST<br>*   SYSTEM（仅数据库内部使用） |
| SUBPARTITIONING\_TYPE | VARCHAR(5) | 子分区类型<br/>*   RANGE<br/>*   HASH<br/>*   LIST |
| PARTITION\_COUNT | BIGINT | 分区数 |
| PARTITIONING\_KEY\_COUNT | INTEGER | 分区键数量 |
| SUBPARTITIONING\_KEY\_COUNT | INTEGER | 子分区键数量 |
| LOCALITY | VARCHAR(6) | 分区索引类型<br>* LOCAL<br>* GLOBAL |
| DEF\_TABLESPACE\_NAME | VARCHAR(64) | 增加分区时的默认表空间名 |
| DEF\_PCT\_FREE | INTEGER | 增加分区时的默认PCTFREE，单位为百分比  |
| DEF\_INI\_TRANS | INTEGER | 增加分区时的默认INI\_TRANS，单位为个数（页面初始化事务） |
| DEF\_MAX\_TRANS | INTEGER | 增加分区时的默认MAX TRANS，单位为个数（页面允许的最大事务） |
| DEF\_LOGGING | CHAR(1) | 增加分区时的默认LOGGING属性，取值为Y或N |
