本视图显示所有节点MEX内存池EDEN部分的信息。

|  字段| 类型| 说明|
|----------------------|--------|----------------------------------------------------------------------------------------------------|
| GROUP_ID             | NUMBER | 组ID                                                                                                |
| GROUP_NODE_ID        | NUMBER | 组内节点ID                                                                                             |
| INST_ID              | NUMBER | 实例ID                                                                                               |
| HOLD_SIZE           | BIGINT | 持有的内存（单位：字节），由参数MEX_POOL_SIZE，WORK_AREA_HEAP_SIZE，WORK_AREA_POOL_SIZE，MAX_SESSIONS，PQ_POOL_SIZE控制 |
| USING_SIZE          | BIGINT | 使用中的内存（单位：字节）                                                                                      |
| UNUSED_SIZE         | BIGINT | 未使用的内存（单位：字节）                                                                                      |
| EMAP_SIZE           | BIGINT | 元数据索引的大小（单位：字节），固定值，跟HOLD_SIZE有关                                                                  |
| EXTENT_HOLD_SIZE   | BIGINT | 元数据持有的内存（单位：字节）                                                                                    |
| EXTENT_USING_SIZE  | BIGINT | 元数据使用的内存（单位：字节）                                                                                    |
| EXTENT_UNUSED_SIZE | BIGINT | 元数据未使用的内存（单位：字节）                                                                                   |
