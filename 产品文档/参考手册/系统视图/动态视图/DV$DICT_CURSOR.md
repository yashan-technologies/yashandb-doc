本视图显示分布式集群中所有节点正在被使用的游标信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SID | INTEGER | 会话ID |
| GLOBAL | VARCHAR(8) | 是否全局游标<br>\*   TRUE<br>\*   FALSE |
| SQL_ID | VARCHAR(13) | 打开游标的SQL ID<br>\* -1：默认SQL ID，刚申请到还未使用的游标<br>\* -2：存储内部打开系统表的游标<br>\* -3：存储内部打开用户表的游标<br>\* other：通过SQL打开的游标，与V$SQLTEXT联合查询可得到对应的SQL语句 |
| TYPE | VARCHAR(32) | 游标的类型，标识游标打开的对象类型 |
| CONTEXT | VARCHAR(2048) | 游标相关的上下文信息，为预留字段，默认为空 |
