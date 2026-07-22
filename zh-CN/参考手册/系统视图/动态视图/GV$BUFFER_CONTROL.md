本视图显示数据缓存区页面控制信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| ADDR  |  RAW(8) | buffer control内存地址 |
| PART | INTEGER | buffer control所在buffer part区域 |
| ID | INTEGER | buffer control编号 |
| IN\_OLD | BOOLEAN | buffer control是否为OLD |
| LIST\_ID | INTEGER | LRU链编号 |
| HASH\_NEXT | INTEGER | 所在bucket的下一个buffer control |
| CR\_NEXT | INTEGER | 下一个CR buffer control |
| LRU\_NEXT | INTEGER | LRU链上下一个buffer control |
| LRU\_PREV | INTEGER | LRU链上前一个buffer control |
| BUCKET\_ID | INTEGER | BUCKET ID |
| TS# | INTEGER | buffer加载页面的表空间ID |
| FILE# | INTEGER | buffer加载页面的文件ID |
| BLK# | INTEGER | buffer加载页面的页面ID |
| DIRTY | BOOLEAN | 是否是脏页 |
| LOAD\_STATUS | INTEGER | buffer页面加载状态<br>\* 0：BP\_NEED\_LOAD，需要读盘加载block<br>\* 1：BP\_AIM\_LOADING，正在加载aim block<br>\* 2：BP\_IS\_LOADED，完成block的加载<br>\* 3：BP\_LOAD\_FAILED，最近一次加载block失败<br>\* 4：BP\_IS\_RECYCLING，buffer ctrl处于淘汰过程中 |
| RES\_STATUS | INTEGER | 集群下buffer页面的资源状态<br>\*   0：BP\_RES\_FREE，当前Buffer Ctrl处于空闲状态<br>\*   1：BP\_RES\_CR，当前Buffer Ctrl承载着一个CR页面<br>\*   2：BP\_RES\_SHARED，当前Buffer Ctrl在GCS中登记为共享只读状态<br>\*   3：BP\_RES\_EXCLUSIVE，当前Buffer Ctrl在GCS中登记为独占可写状态 |
| REF\_COUNT | INTEGER | buffer control当前访问的并发数 |
| PAST\_COPY | INTEGER | buffer control是否为past copy<br>\*   0：BP\_NO\_PASTCOPY，非pastcopy<br>\*   1：BP\_IS\_PASTCOPY，是pastcopy |
| REMOTE\_CR\_STATS | INTEGER | 远程请求CR block的次数统计 |
| FLAGS | RAW(8) | buffer control标记 |
| BLK\_ADDR | RAW(8) | block的内存地址 |
| CR\_SCN | BIGINT | CR页面的SCN |
| CR\_XID\_EXT | INTEGER | CR页面事务所在extent |
| CR\_XID\_NODE | INTEGER | CR页面事务所在node |
| CR\_XID\_XSN | INTEGER | CR页面事务的序列号 |
| CR\_SSN | INTEGER | CR页面的SSN |
| LAST\_LFN | BIGINT | redo刷盘序号 |
| DIRTY\_PREV | RAW(8) | 前一个脏页 |
| DIRTY\_NEXT| RAW(8) | 后一个脏页 |
| TRUNC\_LFN | BIGINT | redo刷盘序号 |
| TRUNC\_RST | INTEGER | HA故障次数 |
| TRUNC\_ASN | INTEGER | 归档序列号 |
| TRUNC\_BID | INTEGER | redo块号 |
| RES\_ROLE | VARCHAR(8) | 集群下buffer页面的资源角色<br>\* LOCAL：本地角色<br>\* GLOBAL：全局角色 |
| WRITE\_INST | INTEGER | 当前正在将该页面刷盘的实例ID |
| OBJ | BIGINT | 当前页面所属的对象ID |
