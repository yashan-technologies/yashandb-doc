本视图显示实例当前所有会话的统计信息。

与V$SYSSTAT区别：V$SYSSTAT记录的是所有会话的累计值，V$SESSTAT记录的是分会话ID的统计值。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
|INST_ID|NUMBER| 实例ID|
| SID | SMALLINT | 会话ID |
| STATISTIC# | INTEGER | 统计项ID， 标识每一个统计项，可以通过STATISTIC#在V$STATNAME视图中查找对应统计项的具体名称 |
| VALUE | BIGINT | 统计值 |
