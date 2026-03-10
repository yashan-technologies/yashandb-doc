本视图展现后台异步预读队列情况。

|  字段| 类型| 说明|
|----------------------------|---------------|---------------------------------------------------------|
| ID | INTEGER |异步预读队列ID |
| REQUEST_TIME | BIGINT | 下发异步预取请求时，异步队列接收预读请求的耗时|
| REQUEST_COUNT | BIGINT | 下发异步预取请求时，异步队列接收预读请求的次数|
| REQUEST_BLOCK_COUNT | BIGINT | 下发异步预取请求时，异步队列接收预读请求的block总个数 |
| QUEUE_HEAD | INTEGER  | 异步队列队头槽位ID |
| QUEUE_TAIL| INTEGER | 异步队列队尾槽位ID |
| QUEUE_SORT_TAIL | INTEGER | 异步队列排序队尾槽位ID |
| QUEUE_LEN | INTEGER | 异步队列长度 |
