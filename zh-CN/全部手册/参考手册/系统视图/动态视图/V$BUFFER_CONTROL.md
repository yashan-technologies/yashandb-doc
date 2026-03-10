本视图显示数据缓存区页面控制信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ADDR  |  RAW(8) | buffer control内存地址 |
| PART | INTEGER | buffer control所在buffer part区域 |
| ID | INTEGER | buffer control编号 |
| IN_OLD | BOOLEAN | buffer control是否为OLD |
| LIST_ID | INTEGER | LRU链编号 |
| HASH_NEXT | INTEGER | 所在bucket的下一个buffer control |
| CR_NEXT | INTEGER | 下一个CR buffer control |
| LRU_NEXT | INTEGER | LRU链上下一个buffer control |
| LRU_PREV | INTEGER | LRU链上前一个buffer control |
| BUCKET_ID | INTEGER | BUCKET ID |
| TS# | INTEGER | buffer加载页面的表空间ID |
| FILE# | INTEGER | buffer加载页面的文件ID |
| BLK# | INTEGER | buffer加载页面的页面ID |
| DIRTY | BOOLEAN | 是否是脏页 |
| LOAD_STATUS | INTEGER | buffer页面加载状态<br>\* 0：BP_NEED_LOAD，需要读盘加载block<br>\* 1：BP_AIM_LOADING，正在加载aim block<br>\* 2：BP_IS_LOADED，完成block的加载<br>\* 3：BP_LOAD_FAILED，最近一次加载block失败<br>\* 4：BP_IS_RECYCLING，buffer ctrl处于淘汰过程中 |
| RES_STATUS | INTEGER | 集群下buffer页面的资源状态<br>\*   0：BP_RES_FREE，当前Buffer Ctrl处于空闲状态<br>\*   1：BP_RES_CR，当前Buffer Ctrl承载着一个CR页面<br>\*   2：BP_RES_SHARED，当前Buffer Ctrl在GCS中登记为共享只读状态<br>\*   3：BP_RES_EXCLUSIVE，当前Buffer Ctrl在GCS中登记为独占可写状态 |
| REF_COUNT | INTEGER | buffer control当前访问的并发数 |
| PAST_COPY | INTEGER | buffer control是否为past copy<br>\*   0：BP_NO_PASTCOPY，非pastcopy<br>\*   1：BP_IS_PASTCOPY，是pastcopy |
| REMOTE_CR_STATS | INTEGER | 远程请求CR block的次数统计 |
| FLAGS | RAW(8) | buffer control标记 |
| BLK_ADDR | RAW(8) | block的内存地址 |
| CR_SCN | BIGINT | CR页面的SCN |
| CR_XID_EXT | INTEGER | CR页面事务所在extent |
| CR_XID_NODE | INTEGER | CR页面事务所在node |
| CR_XID_XSN | INTEGER | CR页面事务的序列号 |
| CR_SSN | INTEGER | CR页面的SSN |
| LAST_LFN | BIGINT | redo刷盘序号 |
| DIRTY_PREV | RAW(8) | 前一个脏页 |
| DIRTY_NEXT| RAW(8) | 后一个脏页 |
| TRUNC_LFN | BIGINT | redo刷盘序号 |
| TRUNC_RST | INTEGER | HA故障次数 |
| TRUNC_ASN | INTEGER | 归档序列号 |
| TRUNC_BID | INTEGER | redo块号 |
| RES_ROLE | VARCHAR(8) | 集群下buffer页面的资源角色<br>\* LOCAL：本地角色<br>\* GLOBAL：全局角色 |
| WRITE_INST | INTEGER | 当前正在将该页面刷盘的实例ID |
| OBJ | BIGINT | 当前页面所属的对象ID |
