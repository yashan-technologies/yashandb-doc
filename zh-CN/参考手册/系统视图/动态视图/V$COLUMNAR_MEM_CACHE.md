本视图显示列式存储稳态数据的内存缓存信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| CACHE\_ID | INTEGER | 缓存块编号  |
| QUEUE\_ID | SMALLINT | 缓存块所在的队列编号 |
| DATAOBJ | BIGINT  | 缓存对象的数据对象ID |
| SLICE\_ID | BIGINT | 缓存对象的slice编号 |
| COLUMN\_ID | SMALLINT | 缓存对象的列编号 |
| IS\_META | BOOLEAN | 缓存对象是否为元数据 |
| FLAG | SMALLINT | 缓存标识 |
| RECENT\_HITS | INTEGER | 缓存对象最近命中次数 |
| REFCOUNT | INTEGER | 缓存对象引用次数 |
| OBJSIZE | INTEGER | 缓存对象大小（单位：字节） |
| QUERY\_DIGEST | BIGINT | 加载缓存对象的语句指纹 |
