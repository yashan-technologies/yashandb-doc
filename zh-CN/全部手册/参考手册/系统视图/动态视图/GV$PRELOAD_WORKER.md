本视图展现后台异步预读线程执行情况。

|  字段| 类型| 说明|
|----------------------------|---------------|---------------------------------------------------------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID | INTEGER |预读线程ID |
| SORT_TIME | BIGINT | 异步预读过程中，预读线程对Block请求队列进行排序的耗时 |
| SORT_COUNT | BIGINT | 异步预读过程中，预读线程对Block请求队列进行排序的次数 |
| POP_TIME | BIGINT | 异步预读过程中，预读线程从预读队列摘取请求的耗时 |
| POP_COUNT | BIGINT | 异步预读过程中，预读线程从预读队列摘取请求的次数 |
| READ_TIME | BIGINT | 异步预读过程中，预读线程读取数据文件的IO耗时 |
| READ_COUNT| BIGINT | 异步预读过程中，预读线程读取数据文件的IO次数 |
| READ_BLOCK_COUNT | BIGINT | 异步预读过程中，预读线程从数据文件读取的页面总个数 |
| LOAD_TIME | BIGINT | 异步预读过程中，预读线程请求预读队列里所有Block的加载耗时 |
| LOAD_BLOCK_COUNT | BIGINT | 异步预读过程中，预读线程请求预读队列里所有Block的加载次数 |
| FAIL_COUNT | BIGINT | 异步预读过程中，预读线程加载失败次数 |
| SKIP_COUNT | BIGINT | 异步预读过程中， 跳过过时预读加载次数 |
| LAST_QUEUE | INTEGER | 异步预读线程最近一次执行预读任务的队列ID |
