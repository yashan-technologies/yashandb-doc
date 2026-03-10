该视图描述共享集群后台线程的相关统计信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| TASK_ID | INTEGER | task编号 |
| SESSION_ID | INTEGER | 会话编号 |
| TASK_TYPE | VARCHAR(32) | task类型<br>\*   AXC_GRC_TASK：GRC线程<br>\*   AXC_GCS_TASK：GCS线程<br>\*   AXC_GLS_TASK：GLS线程<br>\*   AXC_REFORM_TASK：reform线程<br>\*   AXC_BATCH_TASK：批处理线程<br>\*   AXC_BCST_TASK：DDL类线程<br>\*   AXC_AUX_TASK：辅助线程<br>\*   AXC_SMON_TASK：死锁检测线程<br>\*   AXC_RCYTEMP_TASK：临时表空间恢复线程 |
| CELL_ID | INTEGER | task所对应的消息队列编号 |
| PROCESS_MESSAGE_NUM | BIGINT | 处理消息的数量 |
| CORE_TASK | BOOLEAN | 是否是核心线程 |
