本视图显示当前session的统计项信息，且显示的是V$SESSTAT的子集，SID对应V$SESSION的SID。

| 字段           | 类型       | 说明  |
|--------------|----------| --- |
| GROUP_ID     | NUMBER   | 组ID |
| GROUP_NODE_ID | NUMBER   | 组内节点ID |
| INST_ID      | NUMBER   | 实例ID|
| SID          | SMALLINT | 当前session ID |
| STATISTIC#   | INTEGER  | 统计项ID， 标识每一个统计项，可以通过STATISTIC#在V$STATNAME视图中查找对应统计项的具体名称 |
| VALUE        | BIGINT   | 统计值 |

