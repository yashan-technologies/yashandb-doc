本视图显示当前所有SESSION LOCK CACHES信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| SID | INTEGER | 会话ID |
| CACHE_TYPE | VARCHAR(16) | LOCK CACHE类型：TABLE CACHE、BLOCK CACHE |
| TOTAL | BIGINT | 产生LOCK CACHE的总数（包含当前存在的数目以及被复用的数目） |
| HITS | BIGINT | LOCK CACHE命中次数 |
| INVALIDS | BIGINT | LOCK CACHE失效次数 |
| MISS | BIGINT | LOCK CACHE未命中次数 |
| COUNT | BIGINT | 当前LOCK CACHE的总数 |
