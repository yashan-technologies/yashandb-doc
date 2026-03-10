本视图显示所有私有临时表的汇总信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID | SMALLINT | 会话ID |
| SERIAL# | BIGINT | 私有临时表的ID，该ID与非临时表的ID独立，因此可能存在非临时表的ID和私有临时表ID相同的情况 |
| OWNER | VARCHAR(64) | 用户名称 |
| TABLE_NAME | VARCHAR(64) | 表名  |
| TABLESPACE_NAME | VARCHAR(64) | 表空间名 |
| DURATION | VARCHAR(16) | 生命周期（会话级、事务级） |
| NUM_ROWS | INTEGER | 表的行数（保留字段） |
| BLOCKS | INTEGER | 表的页面数（保留字段） |
| AVG_ROW_LEN | INTEGER | 平均行数长度（单位：字节）（保留字段） |
| LAST_ANALYZED | DATE | 上次收集统计信息的时间（保留字段） |
| TXN_ID | BIGINT | 创建事务级私有临时表的事务ID（保留字段） |
