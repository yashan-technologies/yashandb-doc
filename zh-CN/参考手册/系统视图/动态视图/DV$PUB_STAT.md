本视图显示分布式MN节点后台推送任务的统计信息。

|  字段| 类型| 说明|
|-----------------| ----------- |------------|
| GROUP\_ID       | INTEGER      | 组ID        |
| GROUP\_NODE\_ID | INTEGER     | 组内节点ID     |
| ID            | BIGINT     | 推送队列序号      |
| USER\_NAME    | VARCHAR(64) | 推送消息的用户     |
| DST\_NODE     | INTEGER     | 推送的目标节点     |
| CREATE\_TIME  | DATE        | 推送任务生成时间    |
| COST\_SECONDS | INTEGER     | 已经执行了多长时间   |
| STATUS        | INTEGER     | 1待推送 2推送中   |
| ERR\_MSG      | VARCHAR(800)   | 推送节点的报错返回信息 |
