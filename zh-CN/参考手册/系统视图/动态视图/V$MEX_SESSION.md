本视图显示当前节点各个会话中内存扩展池的使用情况。

|  字段| 类型| 说明|
|---------------------|----------|-------------------|
| SID                 | SMALLINT | 会话ID              |
| MAX\_HOLD\_SIZE     | BIGINT   | 允许持有的最大内存（单位：字节）  |
| HOLD\_SIZE          | BIGINT   | 持有的内存（单位：字节）      |
| USING\_SIZE         | BIGINT   | 使用中的内存（单位：字节）     |
| UNUSED\_SIZE        | BIGINT   | 未使用的内存（单位：字节）     |
| TOTAL\_ALLOC\_TIMES | BIGINT   | 历史分配次数            |
| TOTAL\_ALLOC\_SIZE  | BIGINT   | 历史分配大小（单位：字节）     |
| TOTAL\_FREE\_TIMES  | BIGINT   | 历史释放次数            |
| TOTAL\_FREE\_SIZE   | BIGINT   | 历史释放大小（单位：字节）     |
| TOTAL\_FILL\_TIMES  | BIGINT   | 历史填充缓存次数（缓存未命中次数） |
| TOTAL\_GC\_TIMES    | BIGINT   | 历史垃圾清理次数          |
