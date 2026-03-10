本视图显示分布式集群中所有节点实例级的内存池信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| NAME | VARCHAR(64) | 内存池的名字<br/>* application pool：公共堆内存池<br/>* sql main pool：SQL缓存池<br/>* sql pl pool：存储过程缓冲池<br/>* dictionary cache：数据字典缓存池 |
| TOTAL_SIZE | BIGINT | 内存池的总大小（单位：字节） |
| BLOCK_SIZE | INTEGER | 内存池中每个block的大小（单位：字节） |
| TOTAL_BLOCK_COUNT | INTEGER | 内存池中总共包含的block数目 |
| FREE_BLOCK_COUNT | INTEGER | 内存池中空闲的block的数目 |
| USED_SIZE | BIGINT | 内存池中被使用的空间大小（单位：字节） |
| FREE_SIZE | BIGINT | 内存池中未被使用的空间大小（单位：字节） |
| BLOCK_HANDLE_CREATE_TIMES | BIGINT | 内存池中的block被创建的次数 |
| BLOCK_HANDLE_DESTROY_TIMES | BIGINT | 内存池中的block被销毁的次数 |
| BLOCK_ALLOC_TIMES | BIGINT | 内存池中的block被分配的次数 |
| POOL_EXTEND_TIMES | INTEGER | 内存池中空间被扩展的次数 |
| POOL_RECYCLE_TIMES | INTEGER | 内存池中空间被回收的次数 |
