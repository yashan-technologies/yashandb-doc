本视图主要描述实例级别的全局应用池、SQL缓存池以及数据字典缓存的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME | VARCHAR(64) | 内存池的名称<br>* application pool：全局应用池<br>* sql main pool：SQL主内存池<br>* sql pl pool：PL内存池<br>* dictionary cache：数据字典缓存 |
| TOTAL\_SIZE | BIGINT | 内存池的总大小（单位：字节） |
| BLOCK\_SIZE | INTEGER | 内存池中每个block的大小（单位：字节） |
| TOTAL\_BLOCK\_COUNT | INTEGER | 内存池中总共包含的block数目 |
| FREE\_BLOCK\_COUNT | INTEGER | 内存池中空闲的block的数目 |
| USED\_SIZE | BIGINT | 内存池中被使用的空间大小（单位：字节） |
| FREE\_SIZE | BIGINT | 内存池中未被使用的空间大小（单位：字节） |
| BLOCK\_HANDLE\_CREATE\_TIMES | BIGINT | 内存池中的block被创建的次数 |
| BLOCK\_HANDLE\_DESTROY\_TIMES | BIGINT | 内存池中的block被销毁的次数 |
| BLOCK\_ALLOC\_TIMES | BIGINT | 内存池中的block被分配的次数 |
| POOL\_EXTEND\_TIMES | INTEGER | 内存池中空间被扩展的次数 |
| POOL\_RECYCLE\_TIMES | INTEGER | 内存池中空间被回收的次数 |
