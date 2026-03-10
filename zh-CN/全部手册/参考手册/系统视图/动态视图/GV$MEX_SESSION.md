本视图显示所有节点各个会话中，MEX内存的使用情况。

|  字段| 类型| 说明|
|---------------------|----------|-------------------|
| GROUP_ID            | NUMBER   | 组ID               |
| GROUP_NODE_ID       | NUMBER   | 组内节点ID            |
| INST_ID             | NUMBER   | 实例ID              |
| SID                 | SMALLINT | 会话ID              |
| MAX_HOLD_SIZE     | BIGINT   | 允许持有的最大内存（单位：字节）  |
| HOLD_SIZE          | BIGINT   | 持有的内存（单位：字节）      |
| USING_SIZE         | BIGINT   | 使用中的内存（单位：字节）     |
| UNUSED_SIZE        | BIGINT   | 未使用的内存（单位：字节）     |
| TOTAL_ALLOC_TIMES | BIGINT   | 历史分配次数            |
| TOTAL_ALLOC_SIZE  | BIGINT   | 历史分配大小（单位：字节）     |
| TOTAL_FREE_TIMES  | BIGINT   | 历史释放次数            |
| TOTAL_FREE_SIZE   | BIGINT   | 历史释放大小（单位：字节）     |
| TOTAL_FILL_TIMES  | BIGINT   | 历史填充缓存次数（缓存未命中次数） |
| TOTAL_GC_TIMES    | BIGINT   | 历史垃圾清理次数          |
