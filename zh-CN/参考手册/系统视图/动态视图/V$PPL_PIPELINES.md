执行流水线并行视图，显示当前执行SQL的执行流水线信息和依赖关系。

|  字段| 类型| 说明|
| --- | --- | --- |
|  SID | SMALLINT | 会话ID |
| GLOBAL_SESSION_ID | INTEGER | 全局会话ID |
| SQL_ID | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| HASH_VALUE | BIGINT | 一个执行计划的唯一标识 |
| PPL_ID | SMALLINT | 在PIPELINE并行执行模型下，一个执行计划内的pipeline标识 |
| PIPELINE | VARCHAR(4000) | 各PIPELINE的信息，输出各算子的plan_type以及plan_id，格式：PX RECEIVER(source)(27)queue id(3) -> HASH JOIN RIGHT SEMI(sink)(11) |
| DEP_IDS | VARCHAR(8000) | 当前PIPELINE依赖的PPL_ID列表，格式：1,2,3 |
