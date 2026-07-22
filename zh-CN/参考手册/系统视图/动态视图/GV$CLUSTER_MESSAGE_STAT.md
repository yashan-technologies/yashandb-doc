本视图显示共享集群消息交互统计信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| ID | INTEGER | 消息序号 |
| NAME | VARCHAR(32) | 消息名称 |
| MESSAGE\_GROUP | VARCHAR(32) | 消息分组<br>\*   AXC\_FG\_TASK：前台线程<br>\*   AXC\_GRC\_TASK：GRC线程<br>\*   AXC\_GCS\_TASK：GCS线程<br>\*   AXC\_GLS\_TASK：GLS线程<br>\*   AXC\_REFORM\_TASK：reform线程<br>\*   AXC\_BATCH\_TASK：批处理线程<br>\*   AXC\_BCST\_TASK：DDL类线程<br>\*   AXC\_AUX\_TASK：辅助线程<br>\*   AXC\_SYNC\_TASK：同步线程<br>\*   AXC\_SMON\_TASK：死锁检测线程<br>\*   AXC\_RCYTEMP\_TASK：临时表空间恢复线程 |
| SEND\_TIMES | BIGINT | 消息发送成功次数 |
| SEND\_FAILED\_TIMES | BIGINT | 消息发送失败次数 |
| SEND\_TOTAL\_COSTS | BIGINT | 消息发送成功总耗时（单位：微秒） |
| SEND\_AVG\_COST | INTEGER | 消息发送成功平均耗时（单位：微秒） |
| SEND\_MAX\_COST | INTEGER | 消息发送成功最长耗时（单位：微秒） |
| RECV\_TIMES | BIGINT | 消息接收次数 |
| RECV\_TOTAL\_COSTS | BIGINT | 消息接收总耗时（单位：微秒） |
| RECV\_AVG\_COST | INTEGER | 消息接收平均耗时（单位：微秒） |
| RECV\_MAX\_COST | INTEGER | 消息接收最长耗时（单位：微秒） |
| WAIT\_TIMES | BIGINT | 异步消息等待处理的次数 |
| WAIT\_TOTAL\_COSTS | BIGINT | 异步消息排队总耗时（单位：微秒） |
| WAIT\_AVG\_COST | INTEGER | 异步消息排队平均耗时（单位：微秒） |
| WAIT\_MAX\_COST | INTEGER | 异步消息排队最长耗时（单位：微秒） |
| PROCESS\_TIMES | BIGINT | 消息处理成功的次数 |
| PROCESS\_FAILED\_TIMES | BIGINT | 消息处理失败的次数 |
| PROCESS\_TOTAL\_COSTS | BIGINT | 消息处理成功的总耗时（单位：微秒） |
| PROCESS\_AVG\_COST | INTEGER | 消息处理成功的平均耗时（单位：微秒） |
| PROCESS\_MAX\_COST | INTEGER | 消息处理成功最长耗时（单位：微秒） |
