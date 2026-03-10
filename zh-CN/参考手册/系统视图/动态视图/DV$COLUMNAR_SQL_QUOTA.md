本视图显示分布式集群中所有节点向量化计算过程SQL使用的配额信息。

|  字段| 类型| 说明|
|----------------------|-------------|-----------------|
| GROUP_ID            | INTEGER     | 组ID             |
| GROUP_NODE_ID      | INTEGER     | 组内节点ID          |
| SQL_ID              | VARCHAR(13) | 唯一标识一条SQL语句的ID值 |
| ESTIMATE             | BIGINT      | 预估配额大小          |
| LOW_LEVEL           | BIGINT      | 配额下限	           |
| UP_LEVEL            | BIGINT      | 配额上限            |
| FREE_UP_LEVEL      | BIGINT      | 预留配额的最大值        |
| MAX_USED            | BIGINT      | 最大使用配额	         |
| USED                 | BIGINT      | 当前使用配额	         |
