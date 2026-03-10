本视图显示向量化计算过程PLAN使用的配额信息。

|  字段| 类型| 说明|
|----------------|-------------|-----------------|
| GROUP_ID       | NUMBER      | 组ID             |
| GROUP_NODE_ID  | NUMBER      | 组内节点ID          |
| INST_ID       | NUMBER      | 实例ID            |
| SQL_ID        | VARCHAR(13) | 唯一标识一条SQL语句的ID值 |
| STAGE_ID      | INTEGER     | SQL中STAGE的ID	   |
| PLAN_ID       | INTEGER     | SQL中PLAN的ID     |
| NAME           | VARCHAR(16) | SQL中PLAN的名称     |
| ESTIMATE       | BIGINT      | 预估配额大小          |
| LOW_LEVEL     | BIGINT      | 配额下限	           |
| UP_LEVEL      | BIGINT      | 配额上限            |
| DEGREE         | SMALLINT    | 并行度	            |
| MIN_UP_LEVEL | BIGINT      | 算子最小的配额上限	      |
| EXPEND         | BOOLEAN     | 是否可以扩充配额	       |
| MAX_USED      | BIGINT      | 最大使用配额	         |
| USED           | BIGINT      | 当前使用配额	         |
