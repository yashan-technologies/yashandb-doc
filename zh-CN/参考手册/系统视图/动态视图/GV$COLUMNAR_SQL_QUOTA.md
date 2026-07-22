本视图显示向量化计算过程SQL使用的配额信息。

|  字段| 类型| 说明|
|-----------------|-------------|-----------------|
| GROUP_ID        | NUMBER      | 组ID             |
| GROUP_NODE_ID   | NUMBER      | 组内节点ID          |
| INST\_ID        | NUMBER      | 实例ID            |
| SQL\_ID         | VARCHAR(13) | 唯一标识一条SQL语句的ID值 |
| ESTIMATE        | BIGINT      | 预估配额大小          |
| LOW\_LEVEL      | BIGINT      | 配额下限	           |
| UP\_LEVEL       | BIGINT      | 配额上限            |
| FREE\_UP\_LEVEL | BIGINT      | 预留配额的最大值        |
| MAX\_USED       | BIGINT      | 最大使用配额	         |
| USED            | BIGINT      | 当前使用配额	         |
