本视图展现后台异步预读队列情况。

|  字段| 类型| 说明|
|----------------------------|---------------|---------------------------------------------------------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| ID | INTEGER |异步预读队列ID |
| REQUEST\_TIME | BIGINT | 下发异步预取请求时，异步队列接收预读请求的耗时|
| REQUEST\_COUNT | BIGINT | 下发异步预取请求时，异步队列接收预读请求的次数|
| REQUEST\_BLOCK\_COUNT | BIGINT | 下发异步预取请求时，异步队列接收预读请求的block总个数 |
| QUEUE\_HEAD | INTEGER  | 异步队列队头槽位ID |
| QUEUE\_TAIL| INTEGER | 异步队列队尾槽位ID |
| QUEUE\_SORT\_TAIL | INTEGER | 异步队列排序队尾槽位ID |
| QUEUE\_LEN | INTEGER | 异步队列长度 |
