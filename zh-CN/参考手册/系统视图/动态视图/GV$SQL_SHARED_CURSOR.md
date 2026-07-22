本视图记录SQL子游标不能共享的原因信息。当SQL语句由于某些原因不能共享子游标时，系统会创建新的子游标，该视图用于诊断游标无法共享的具体原因。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
| SQL_ID | VARCHAR(13) | SQL标识符 |
| ADDRESS | RAW(8) | 父游标的地址 |
| CHILD_ADDRESS | RAW(8) | 子游标的地址 |
| CHILD_NUMBER | INTEGER | 子游标编号 |
| OPTIMIZER_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为优化器环境信息不匹配<br/>* N：表明并非此因素 |
| STATS_ROW_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为统计信息不匹配<br/>* N：表明并非此因素 |
| BIND_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为绑定元数据与现有子游标不匹配<br/>* N：表明并非此因素 |
| TRANSLATION_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为现有子游标的基础对象不匹配<br/>* N：表明并非此因素 |
| BIND_EQUIV_FAILURE | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为绑定值的选择性不匹配<br/>* N：表明并非此因素 |
| PX_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为影响并行化的参数不匹配<br/>* N：表明并非此因素 |
| INST_MISMATCH | VARCHAR(1) | 该因素是否为导致SQL子游标无法共享的原因之一：<br/>* Y：表明是此因素，具体缘由为集群实例映射不匹配<br/>* N：表明并非此因素 |
