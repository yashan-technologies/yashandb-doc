本视图显示所有节点MEX内存池BASE部分的信息。

|  字段| 类型| 说明|
|-----------------------|--------|----------------|
| GROUP_ID              | NUMBER | 组ID            |
| GROUP_NODE_ID         | NUMBER | 组内节点ID         |
| INST_ID               | NUMBER | 实例ID           |
| HOLD_SIZE            | BIGINT | 持有的内存（单位：字节）   |
| USING_SIZE           | BIGINT | 使用中的内存（单位：字节）  |
| UNUSED_SIZE          | BIGINT | 未使用的内存（单位：字节）  |
| FILL_TIMES           | BIGINT | 填充的次数（缓存未命中次数） |
| MAX_CONTINUOUS_SIZE | BIGINT | 最大连续内存（单位：字节）  |
