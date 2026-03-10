本视图显示列式存储稳态数据的内存缓存信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| CACHE_ID | INTEGER | 缓存块编号  |
| QUEUE_ID | SMALLINT | 缓存块所在的队列编号 |
| DATAOBJ | BIGINT  | 缓存对象的数据对象ID |
| SLICE_ID | BIGINT | 缓存对象的slice编号 |
| COLUMN_ID | SMALLINT | 缓存对象的列编号 |
| IS_META | BOOLEAN | 缓存对象是否为元数据 |
| FLAG | SMALLINT | 缓存标识 |
| RECENT_HITS | INTEGER | 缓存对象最近命中次数 |
| REFCOUNT | INTEGER | 缓存对象引用次数 |
| OBJSIZE | INTEGER | 缓存对象大小（单位：字节） |
| QUERY_DIGEST | BIGINT | 加载缓存对象的语句指纹 |
