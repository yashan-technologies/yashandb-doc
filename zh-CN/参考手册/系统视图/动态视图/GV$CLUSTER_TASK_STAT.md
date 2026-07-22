该视图描述共享集群后台线程的相关统计信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| TASK\_ID | INTEGER | task编号 |
| SESSION\_ID | INTEGER | 会话编号 |
| TASK\_TYPE | VARCHAR(32) | task类型<br>\*   AXC\_GRC\_TASK：GRC线程<br>\*   AXC\_GCS\_TASK：GCS线程<br>\*   AXC\_GLS\_TASK：GLS线程<br>\*   AXC\_REFORM\_TASK：reform线程<br>\*   AXC\_BATCH\_TASK：批处理线程<br>\*   AXC\_BCST\_TASK：DDL类线程<br>\*   AXC\_AUX\_TASK：辅助线程<br>\*   AXC\_SMON\_TASK：死锁检测线程<br>\*   AXC\_RCYTEMP\_TASK：临时表空间恢复线程 |
| CELL\_ID | INTEGER | task所对应的消息队列编号 |
| PROCESS\_MESSAGE\_NUM | BIGINT | 处理消息的数量 |
| CORE\_TASK | BOOLEAN | 是否是核心线程 |
