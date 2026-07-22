故障诊断视图，显示所有健康检查相关信息及其状态。

|  字段| 类型| 说明|
| --- | --- | --- |
| RUN\_ID | INTEGER | 表示运行的唯一ID |
| NAME | VARCHAR(33) | 用于标识运行的唯一名称 |
| CHECK\_NAME | VARCHAR(33) | 检查项的名称 |
| RUN\_MODE | VARCHAR(8) | 运行方式 <br>\*   MANUAL：手动执行高级包<br>\*   REACTIVE：数据库自动触发 |
| START\_TIME | DATE | 运行的开始时间 |
| END\_TIME | DATE | 运行的结束时间 |
| STATUS | VARCHAR(11) | 运行状态 <br>\*   INITIAL：初始化文件<br>\*   EXECUTING：正在执行检查<br>\*   ERROR：检查出现异常<br>\*   COMPLETED：检查完成 |
| NUM\_INCIDENT | INTEGER | 此运行创建的事件数 |
| ERROR\_NUMBER | INTEGER | 错误号（由于错误导致运行无法完成） |
