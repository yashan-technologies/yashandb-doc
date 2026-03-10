本视图显示分布式MN节点后台推送任务的统计信息。

|  字段| 类型| 说明|
|-----------------| ----------- |------------|
| GROUP_ID       | INTEGER      | 组ID        |
| GROUP_NODE_ID | INTEGER     | 组内节点ID     |
| ID            | BIGINT     | 推送队列序号      |
| USER_NAME    | VARCHAR(64) | 推送消息的用户     |
| DST_NODE     | INTEGER     | 推送的目标节点     |
| CREATE_TIME  | DATE        | 推送任务生成时间    |
| COST_SECONDS | INTEGER     | 已经执行了多长时间   |
| STATUS        | INTEGER     | 1待推送 2推送中   |
| ERR_MSG      | VARCHAR(800)   | 推送节点的报错返回信息 |
