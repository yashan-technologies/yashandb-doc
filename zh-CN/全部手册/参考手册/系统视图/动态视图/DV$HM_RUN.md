故障诊断视图，显示存算一体分布式集群中所有节点的所有健康检查相关信息及其状态。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| RUN_ID | INTEGER | 标识运行的唯一ID |
| NAME | VARCHAR(33) | 标识运行的唯一名称 |
| CHECK_NAME | VARCHAR(33) | 检查项的名称 |
| RUN_MODE | VARCHAR(8) | 运行方式 <br>\*   MANUAL：手动执行高级包<br>\*   REACTIVE：数据库自动触发 |
| START_TIME | DATE | 运行的开始时间 |
| END_TIME | DATE | 运行的结束时间 |
| STATUS | VARCHAR(11) | 运行状态 <br>\*   INITIAL：初始化文件<br>\*   EXECUTING：正在执行检查<br>\*   ERROR：检查出现异常<br>\*   COMPLETED：检查完成 |
| NUM_INCIDENT | INTEGER | 此运行创建的事件数 |
| ERROR_NUMBER | INTEGER | 错误号（由于错误导致运行无法完成） |
