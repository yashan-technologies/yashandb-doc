本视图显示checkpoint的相关信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| TOTAL_NUM | BIGINT | 总的checkpoint次数 |
| SCHEDULE_NUM | BIGINT | 条件触发的checkpoint次数 |
| LAST_EXECUTED | DATE | 上次执行checkpoint的时间 |
| CURRENT_STATUS |  VARCHAR(16) | 当前的状态<br>\* FULL：全量<br>\* INCREMENTAL：增量<br>\* IDLE：空闲 |
| DIRTY_QUEUE_LENGTH | BIGINT | checkpoint脏块队列的长度 |
| DIRTY_QUEUE_FIRST |  VARCHAR(32) | checkpoint脏块队列的队首block |
| DIRTY_QUEUE_LAST |  VARCHAR(32) | checkpoint脏块队列的队尾block |
| TRUNC_POINT |  VARCHAR(32) | checkpoint脏块队列日志截断点 |