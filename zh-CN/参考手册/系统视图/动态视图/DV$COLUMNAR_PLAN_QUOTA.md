本视图显示存算一体分布式集群中所有节点向量化计算过程PLAN使用的配额信息。

|  字段| 类型| 说明|
|-----------------|-------------|-----------------|
| GROUP\_ID       | INTEGER     | 组ID             |
| GROUP\_NODE\_ID | INTEGER     | 组内节点ID          |
| SQL\_ID         | VARCHAR(13) | 唯一标识一条SQL语句的ID值 |
| STAGE\_ID       | INTEGER     | SQL中STAGE的ID	   |
| PLAN\_ID        | INTEGER     | SQL中PLAN的ID     |
| NAME            | VARCHAR(16) | SQL中PLAN的名称     |
| ESTIMATE        | BIGINT      | 预估配额大小          |
| LOW\_LEVEL      | BIGINT      | 配额下限	           |
| UP\_LEVEL       | BIGINT      | 配额上限            |
| DEGREE          | SMALLINT    | 并行度	            |
| MIN\_UP\_LEVEL  | BIGINT      | 算子最小的配额上限	      |
| EXPEND          | BOOLEAN     | 是否可以扩充配额	       |
| MAX\_USED       | BIGINT      | 最大使用配额	         |
| USED            | BIGINT      | 当前使用配额	         |
