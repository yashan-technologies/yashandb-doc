本视图显示存算一体分布式集群中所有节点上执行和等待的任务信息。

|  字段| 类型| 说明|
|-----------------| --- | --- |
| GROUP_ID       | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID              | BIGINT | 任务ID |
| PARENT_ID      | BIGINT | 父任务ID |
| TYPE            | VARCHAR(32) | 当前任务的类型 |
| PARENT_STEP    | TINYINT | 在父任务中的STEP值
| CURRENT_STEP   | TINYINT | 当前任务正在进行的步骤
| TOTAL_STEP     | TINYINT | 当前任务的总步骤数
| STATUS          | VARCHAR(32) | 任务状态
| DATA            | JSON | 数据内容
| LAST_ERROR     | VARCHAR(128) | 最后错误信息
| MAX_TRY_TIMES | TINYINT | 重试次数
| FAIL_TIMES     | TINYINT | 失败次数
| CREATE_TIME    | TIMESTAMP | 任务创建时间
| UPDATE_TIME    | TIMESTAMP | 任务刷新时间
| EXECUTE_TIMES | TINYINT    | 执行次数 |
