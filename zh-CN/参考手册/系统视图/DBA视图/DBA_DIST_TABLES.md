从v23.2.1起，本视图已弃用。原用于显示所有的分布表信息。

|  字段| 类型| 说明|
| --- |---------------| --- |
| OWNER | VARCHAR(64)   | 表所属用户名 |
| TABLE\_NAME | VARCHAR(64)   | 表名 |
| DIST_TYPE | VARCHAR(10)   | 分布类型<br/>\* RANGE<br/>\* HASH<br/>\* LIST<br/>\* ROUNDRROBIN |
| DIST_KEY_COUNT | INTEGER | 分布键个数                                                   |
