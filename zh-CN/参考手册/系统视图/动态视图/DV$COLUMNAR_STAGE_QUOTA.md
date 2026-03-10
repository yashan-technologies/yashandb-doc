本视图显示分布式集群中所有节点向量化计算过程STAGE使用的配额信息。

|  字段| 类型| 说明|
|----------------------|-------------|-----------------|
| GROUP_ID            | INTEGER     | 组ID             |
| GROUP_NODE_ID      | INTEGER     | 组内节点ID          |
| SQL_ID              | VARCHAR(13) | 唯一标识一条SQL语句的ID值 |
| STAGE_ID            | INTEGER     | SQL中STAGE的ID	   |
| PLAN_ID             | INTEGER     | SQL中PLAN的ID     |
| ESTIMATE             | BIGINT      | 预估配额大小          |
| LOW_LEVEL           | BIGINT      | 配额下限	           |
| UP_LEVEL            | BIGINT      | 配额上限            |
| FREE_UP_LEVEL      | BIGINT      | 空闲配额上限          |
| PLAN_MIN_UP_LEVEL | BIGINT      | 算子最小的配额上限       |
| MAX_USED            | BIGINT      | 最大使用配额	         |
| USED                 | BIGINT      | 当前使用配额	         |
| BULK_SIZE            | INTEGER     | 每批次记录行数	         |
