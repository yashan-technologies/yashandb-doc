本视图显示当前节点上所有会话的私有内存区域信息。

|  字段| 类型| 说明|
| --- | --- | --- |
|  SID | SMALLINT | 会话ID |
| TYPE | VARCHAR(32) | 内存类型 <br/>* vm buffer pool：当前会话使用的行执行虚拟内存，动态从共享内存区域分配 <br/>* large pool：当前会话使用的大对象池，动态从共享内存区域分配  <br/>* app pool：当前会话私有的应用池<br/>* pq pool：当前会话使用的并行查询池，动态从内存扩展池分配   <br/>* columnar vm buffer pool：当前会话私有的列执行虚拟内存   |
| USE_SIZE | BIGINT | 当前使用中的内存容量（单位：字节）  |
