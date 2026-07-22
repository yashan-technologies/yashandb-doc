本视图显示所有子分区模板的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| USER_NAME | VARCHAR(64) | 表的用户名 |
| TABLE\_NAME | VARCHAR(64) | 分区所属表名 |
| SUBPARTITION_NAME | VARCHAR(64) | 子分区名 |
| SUBPARTITION_POSITION | BIGINT | 子分区在表内的位置 |
| TABLESPACE\_NAME | VARCHAR(64) | 子分区所在表空间名称 |
| HIGH_BOUND | VARCHAR(4000) | 子分区边界值字符串 |
