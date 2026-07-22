本视图显示向量化计算过程STAGE使用的配额信息。

|  字段| 类型| 说明|
|----------------------|--------------|-----------------|
| SQL\_ID              | VARCHAR(13)  | 唯一标识一条SQL语句的ID值 |
| STAGE\_ID            | INTEGER      | SQL中STAGE的ID	   |
| PLAN\_ID             | INTEGER      | SQL中PLAN的ID     |
| ESTIMATE             | BIGINT       | 预估配额大小          |
| LOW\_LEVEL           | BIGINT       | 配额下限	           |
| UP\_LEVEL            | BIGINT       | 配额上限            |
| FREE\_UP\_LEVEL      | BIGINT       | 空闲配额上限          |
| PLAN\_MIN\_UP\_LEVEL | BIGINT       | 算子最小的配额上限       |
| MAX\_USED            | BIGINT       | 最大使用配额	         |
| USED                 | BIGINT       | 当前使用配额	         |
| BULK_SIZE            | INTEGER     | 每批次记录行数	         |
