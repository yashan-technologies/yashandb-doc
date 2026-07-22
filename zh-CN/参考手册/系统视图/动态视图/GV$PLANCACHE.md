本视图用于检测集群中所有实例plan cache的使用情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| SQL\_POOL\_ID | INTEGER | SQL缓存池ID |
| SQL\_PART\_ID | INTEGER | SQL缓存池分区ID |
| MPOOL\_NAME | VARCHAR(64) | 内存池名称<br>* sql main pool：SQL主内存池<br>* sql pl pool：PL内存池 |
| FREE\_COUNT | INTEGER | 空闲SQL缓存对象数量（保留字段） |
| LRU\_COUNT | INTEGER | LRU中的SQL缓存对象数量 |
| LRU\_COLD\_COUNT | INTEGER | LRU中冷状态的SQL缓存对象数量 |
| LRU\_ADJUST\_TIMES | BIGINT | LRU调整次数 |
| LRU\_ADJUST\_STEPS | BIGINT | LRU调整的总步数 |
| LRU\_RECYCLE\_STEPS | BIGINT | LRU回收的总步数 |
| CREATE\_TOTAL | BIGINT | SQL缓存对象的创建次数 |
| REUSE\_TOTAL | BIGINT | SQL缓存对象的重用次数<br>同一个用户的相同SQL可以尝试复用缓存 |
| INVALIDATE\_TOTAL | BIGINT | SQL缓存对象的失效次数 |
| CLOSE\_TOTAL | BIGINT | SQL缓存对象的关闭次数 |
| RELEASE\_TOTAL | BIGINT | SQL缓存对象的释放次数<br>SQL缓存对象在失效之后，由最后一个关闭的statement进行回收 |
| RECYCLE\_TOTAL | BIGINT | SQL缓存对象的回收次数 |
| CLEAN\_TOTAL | BIGINT | 由对象字典回收触发的不常用的SQL缓存对象的清理<br>只会清理未被使用的SQL缓存对象 |
| CURR\_ACTIVE | INTEGER | 当前正在被使用的SQL缓存对象 |
| CURR\_INACTIVE | INTEGER | 当前未被使用的SQL缓存对象 |
| CURR\_INVALID | INTEGER | 当前已经失效但还有statement未关闭的SQL缓存对象 |
| CURR\_ACTIVE\_BLOCKS | INTEGER | 当前正在被使用的SQL缓存对象所持有的总页面 |
| CURR\_INACTIVE\_BLOCKS | INTEGER | 当前未被使用的SQL缓存对象所持有的总页面 |
| CURR\_INVALID\_BLOCKS | INTEGER | 当前已经失效但还有statement未关闭的SQL缓存对象所持有的总页面 |
