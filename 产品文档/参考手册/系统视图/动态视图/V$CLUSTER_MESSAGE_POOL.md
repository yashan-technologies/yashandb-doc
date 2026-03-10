本视图显示共享集群消息池的概况。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| ID | INTEGER | 消息池ID |
| SIZE | INTEGER | 消息缓存单元的大小 |
| BLOCK_COUNT| INTEGER | 消息池当前使用block的数量 |
| COUNT | INTEGER | 消息池能够存储消息的数量 |
| USE_COUNT | INTEGER | 消息池已经分配的缓存单元数量 |
| FREE_COUNT | INTEGER | 消息池分配出去释放的缓存单元数量 |
| WAIT_TIMES | BIGINT | 申请内存时等待的次数 |
| RECYCLE_TIMES| BIGINT | 申请内存时淘汰其他池的次数 |
