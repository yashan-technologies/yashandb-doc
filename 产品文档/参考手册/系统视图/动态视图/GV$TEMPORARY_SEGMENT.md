本视图显示数据库会话中全局临时表的segment信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID | SMALLINT | 会话ID |
| OBJ# | BIGINT | segment所属对象的ID |
| DATAOID | BIGINT | segment的数据对象ID |
| BO# |  BIGINT | segment所属对象的父对象ID |
| ENTRY | INTEGER | segment入口页 |
| SEGMENT_TYPE |  VARCHAR(16) | segment所属对象类型 |
