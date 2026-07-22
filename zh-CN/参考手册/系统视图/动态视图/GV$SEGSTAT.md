本视图显示segment级别统计信息。

|  字段| 类型| 说明|
|------------------|-------------|------------------|
| GROUP\_ID        | NUMBER      | 组ID             |
| GROUP\_NODE\_ID  | NUMBER      | 组内节点ID       |
| INST\_ID         | NUMBER      | 实例ID           |
| TS#              | INTEGER     | 表空间ID         |
| OBJ#             | BIGINT      | 对象ID           |
| DATAOBJ#         | BIGINT      | 对象DATAOBJ ID   |
| STATISTIC\_NAME  | VARCHAR(64) | 统计信息名       |
| STATISTIC#       | TINYINT     | 统计信息ID       |
| VALUE            | BIGINT      | 统计信息值       |
