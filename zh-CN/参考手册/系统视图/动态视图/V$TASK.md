任务视图，显示当前执行和等待的任务信息。

|  字段| 类型| 说明|
| --- |--------------| --- |
| ID | BIGINT       | 任务ID |
| PARENT\_ID | BIGINT       | 父任务ID |
| TYPE | VARCHAR(32)  | 当前任务的类型 |
| PARENT\_STEP | TINYINT      | 在父任务中的STEP值
| CURRENT\_STEP | TINYINT      | 当前任务正在进行的步骤
| TOTAL\_STEP | TINYINT      | 当前任务的总步骤数
| STATUS | VARCHAR(32)  | 任务状态
| DATA | JSON         | 数据内容
| LAST\_ERROR | VARCHAR(128) | 最后错误信息
| MAX\_TRY\_TIMES | TINYINT      | 重试次数
| FAIL\_TIMES | TINYINT      | 失败次数
| CREATE\_TIME | TIMESTAMP(6)   | 任务创建时间
| UPDATE\_TIME | TIMESTAMP(6)    | 任务刷新时间
| EXECUTE\_TIMES | TINYINT    | 执行次数 |
