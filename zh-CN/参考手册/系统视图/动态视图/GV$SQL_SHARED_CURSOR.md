本视图记录SQL子游标不能共享的原因信息。当SQL语句由于某些原因不能共享子游标时，系统会创建新的子游标，该视图用于诊断游标无法共享的具体原因。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
|  SQL_ID | VARCHAR(13) | SQL标识符 |
| ADDRESS | RAW(8) | 父游标的地址 |
| CHILD_ADDRESS | RAW(8) | 子游标的地址 |
| CHILD_NUMBER | INTEGER | 子游标编号 |
| OPTIMIZER_MISMATCH | VARCHAR(1) | 优化器环境信息不匹配（Y/N） |
| STATS_ROW_MISMATCH | VARCHAR(1) | 统计信息不匹配（Y/N） |
| BIND_MISMATCH | VARCHAR(1) | 绑定元数据与现有子游标不匹配（Y/N） |
| TRANSLATION_MISMATCH | VARCHAR(1) | 现有子游标的基础对象不匹配（Y/N） |
| BIND_EQUIV_FAILURE | VARCHAR(1) | 绑定值的选择性不匹配（Y/N） |
| PX_MISMATCH | VARCHAR(1) | 影响并行化的参数不匹配（Y/N） |
| INST_MISMATCH | VARCHAR(1) | 集群实例映射不匹配（Y/N） |
