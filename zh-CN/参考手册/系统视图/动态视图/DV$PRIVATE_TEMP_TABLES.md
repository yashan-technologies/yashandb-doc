本视图显示存算一体分布式集群中所有节点私有临时表的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| SERIAL# | BIGINT | 私有临时表的ID，该ID与非临时表的ID独立，因此可能存在非临时表的ID和私有临时表ID相同的情况 |
| OWNER | VARCHAR(64) | 用户名称 |
| TABLE\_NAME | VARCHAR(64) | 表名  |
| TABLESPACE\_NAME | VARCHAR(64) | 表空间名 |
| DURATION | VARCHAR(16) | 生命周期（会话级、事务级） |
| NUM\_ROWS | INTEGER | 表的行数 |
| BLOCKS | INTEGER | 表的页面数 |
| AVG\_ROW\_LEN | INTEGER | 平均行数长度（单位：字节） |
| LAST\_ANALYZED | DATE | 上次收集统计信息的时间 |
| TXN\_ID | BIGINT | 创建事务级私有临时表的事务ID |
