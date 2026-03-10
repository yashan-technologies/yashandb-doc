本视图显示向量化计算过程全局使用的配额信息。

| 字段                 | 类型      | 说明          |
|--------------------|---------|-------------|
| GROUP_ID           | NUMBER  | 组ID         |
| GROUP_NODE_ID      | NUMBER  | 组内节点ID      |
| INST_ID           | NUMBER  | 实例ID        |
| TOTAL              | BIGINT  | 总共配额大小      |
| USED               | BIGINT  | 已使用的配额大小    |
| FREE_UP_LEVEL    | BIGINT  | 已分配配额的上限    |
| ACTIVE_SQL_COUNT | BIGINT  | 当前SQL语句的数量  |
| MAX_USED          | BIGINT  | 已使用的最大配额    |