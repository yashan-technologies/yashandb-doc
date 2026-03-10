本视图显示共享集群消息交互统计信息。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| ID | INTEGER | 消息序号 |
| NAME | VARCHAR(32) | 消息名称 |
| MESSAGE_GROUP | VARCHAR(32) | 消息分组<br>\*   AXC_FG_TASK：前台线程<br>\*   AXC_GRC_TASK：GRC线程<br>\*   AXC_GCS_TASK：GCS线程<br>\*   AXC_GLS_TASK：GLS线程<br>\*   AXC_REFORM_TASK：reform线程<br>\*   AXC_BATCH_TASK：批处理线程<br>\*   AXC_BCST_TASK：DDL类线程<br>\*   AXC_AUX_TASK：辅助线程<br>\*   AXC_SYNC_TASK：同步线程<br>\*   AXC_SMON_TASK：死锁检测线程<br>\*   AXC_RCYTEMP_TASK：临时表空间恢复线程 |
| SEND_TIMES | BIGINT | 消息发送成功次数 |
| SEND_FAILED_TIMES | BIGINT | 消息发送失败次数 |
| SEND_TOTAL_COSTS | BIGINT | 消息发送成功总耗时（单位：微秒） |
| SEND_AVG_COST | INTEGER | 消息发送成功平均耗时（单位：微秒） |
| SEND_MAX_COST | INTEGER | 消息发送成功最长耗时（单位：微秒） |
| RECV_TIMES | BIGINT | 消息接收次数 |
| RECV_TOTAL_COSTS | BIGINT | 消息接收总耗时（单位：微秒） |
| RECV_AVG_COST | INTEGER | 消息接收平均耗时（单位：微秒） |
| RECV_MAX_COST | INTEGER | 消息接收最长耗时（单位：微秒） |
| WAIT_TIMES | BIGINT | 异步消息等待处理的次数 |
| WAIT_TOTAL_COSTS | BIGINT | 异步消息排队总耗时（单位：微秒） |
| WAIT_AVG_COST | INTEGER | 异步消息排队平均耗时（单位：微秒） |
| WAIT_MAX_COST | INTEGER | 异步消息排队最长耗时（单位：微秒） |
| PROCESS_TIMES | BIGINT | 消息处理成功的次数 |
| PROCESS_FAILED_TIMES | BIGINT | 消息处理失败的次数 |
| PROCESS_TOTAL_COSTS | BIGINT | 消息处理成功的总耗时（单位：微秒） |
| PROCESS_AVG_COST | INTEGER | 消息处理成功的平均耗时（单位：微秒） |
| PROCESS_MAX_COST | INTEGER | 消息处理成功最长耗时（单位：微秒） |