本视图显示segment级别统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 对象所属用户名 |
| OBJECT\_NAME | VARCHAR(64) | 对象名 |
| SUBOBJECT\_NAME | VARCHAR(64) | 分区或者子分区名 |
| TABLESPACE\_NAME | VARCHAR(64) | 表空间名 |
| TS# | INTEGER | 表空间ID |
| OBJ# | BIGINT | 对象ID |
| DATAOBJ# | BIGINT | 对象DATAOBJ ID |
| OBJECT\_TYPE | VARCHAR(18) | 对象类型名 |
| STATISTIC\_NAME | VARCHAR(64) | 统计信息名 |
| STATISTIC# | TINYINT | 统计信息ID |
| VALUE | BIGINT | 统计信息值 |
