本视图显示所有节点上共享内存池信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
|  NAME | VARCHAR(32) | 内存池名称<br />* SQL POOL：SQL缓存<br />* DICTIONARY CACHE POOL：数据字典缓存<br />* LOCK POOL：锁资源池<br />* CURSOR POOL：游标缓存池<br />* DSTB POOL：分布式内存池（仅存算一体分布式集群中存在）<br />* RECOVERY BUDDY POOL：故障恢复缓冲池<br />* BLOCK RES POOL：全局缓存资源池（仅共享集群/分布式集群中存在）<br />* NON BLOCK RES POOL：全局锁资源池（仅共享集群/分布式集群中存在）<br />* GRC REQ POOL：全局队列资源池（仅共享集群/分布式集群中存在）<br />* GCS PC POOL：全局历史快照资源池（仅共享集群/分布式集群中存在）<br />* GRC OBJECT POOL：全局对象亲和规则资源池（仅共享集群/分布式集群中存在）<br />* GLS POOL：全局锁资源本地缓存池（仅共享集群/分布式集群中存在）<br />* STREAM POOL：逻辑日志解析资源池<br />* GCS LOCK POOL：全局缓存资源锁池（仅共享集群/分布式集群中存在）<br />* FREE POOL：冗余内存 <br />* SEGMENT STATISTICS POOL：段统计缓存池 |
| SIZE | BIGINT | 内存池大小（单位：字节） |
| MEMORY_INIT_TYPE | VARCHAR(16) | 初始化内存池的方式<br>* PERCENTAGE：按照百分比初始化 <br>* NUMBER：按照固定大小初始化 |
| MEMORY_MANAGE_TYPE | VARCHAR(16) | 内存管理方式<br>* VARIABLE：内存可动态伸缩 <br>* FIXED：固定内存大小 |
| MEMORY_REQUEST_COUNT | BIGINT | 内存池动态申请内存的次数 |
| MEMORY_REQUEST_SIZE | BIGINT | 内存池动态申请内存的大小（单位：字节） |
| MEMORY_FREE_COUNT | BIGINT | 内存池动态释放内存的次数 |
| MEMORY_FREE_SIZE | BIGINT | 内存池动态释放内存的大小（单位：字节）   |
