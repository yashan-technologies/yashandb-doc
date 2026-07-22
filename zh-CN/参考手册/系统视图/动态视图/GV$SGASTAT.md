本视图显示所有节点上内存共享池和大对象池的相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| POOL | VARCHAR(32) | 内存池类型<br />* SHARE POOL：内存共享池<br />* LARGE POOL：大对象池          |
| NAME | VARCHAR(32) | 内存共享池的各个子级内存池名称<br />* SQL POOL：SQL缓存<br />* DICTIONARY CACHE POOL：数据字典缓存<br />* LOCK POOL：锁资源池<br />* CURSOR POOL：游标缓存池<br />* DSTB POOL：分布式内存池（仅存算一体分布式集群中存在）<br />* RECOVERY BUDDY POOL：故障恢复缓冲池<br />* BLOCK RES POOL：全局缓存资源池（仅共享集群/分布式集群中存在）<br />* NON BLOCK RES POOL：全局锁资源池（仅共享集群/分布式集群中存在）<br />* GRC REQ POOL：全局队列资源池（仅共享集群/分布式集群中存在）<br />* GCS PC POOL：全局历史快照资源池（仅共享集群/分布式集群中存在）<br />* GRC OBJECT POOL：全局对象亲和规则资源池（仅共享集群/分布式集群中存在）<br />* GLS POOL：全局锁资源本地缓存池（仅共享集群/分布式集群中存在）<br />* STREAM POOL：逻辑日志解析资源池<br />* GCS LOCK POOL：全局缓存资源锁池（仅共享集群/分布式集群中存在）<br />* FREE POOL：冗余内存 <br />* SEGMENT STATISTICS POOL：段统计缓存池<br/><br/> 大对象池的内存信息：<br />* used memory：正在使用中的内存<br />* free memory：空闲的内存        |
| BYTES | BIGINT | 内存池的容量（单位：字节）  |
