本视图显示集群中所有实例上正在运行的JOB信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER | 实例ID |
|  SESSION_ID | SMALLINT | 会话ID |
| SESSION_SERIAL_NUM | INTEGER | 会话序列号 |
| JOB_ID | BIGINT | JOB标识符 |
| PADDR | BIGINT | 线程ID |
| OS_PROCESS_ID | VARCHAR(1) | 操作系统进程ID（当前为NULL） |
| SESSION_STAT_CPU | VARCHAR(1) | 会话CPU统计（当前为NULL） |
| CON_ID | VARCHAR(1) | 容器ID（当前为NULL） |
