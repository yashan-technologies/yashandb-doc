本视图显示最近一次实例恢复任务的信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| IN\_RECOVERY | BOOLEAN | 是否处于故障恢复中 |
| INSTANCES | BIGINT | 恢复实例列表，64位整型值，需转为二进制查看，在二进制中每一位表示一个实例的状态（与INST_ID顺序对应），若某个实例故障则其对应的INSTANCES位的值为1，实例正常则为0 |
| BEGIN\_TIME | TIMESTAMP(6) | 实例恢复开始的时间 |
| GLOBAL\_RESOURCE\_AVAILABLE\_TIME | TIMESTAMP(6) | 全局资源可用时间 |
| COMPLETE\_RECOVERY\_TIME | TIMESTAMP(6) | 实例恢复完全可用的时间 |
| END\_TIME | TIMESTAMP(6) | 实例恢复结束的时间 |
| RECOVERY\_REDO\_SIZE | BIGINT | 实例恢复涉及的redo量（单位：字节） |
| COMPLETE\_REDO\_SIZE | BIGINT | 当前实例恢复已完成的redo量（单位：字节） |
| PHASE | VARCHAR(68) | 当前故障恢复的阶段 |
| TARGET\_MTTR | INTEGER | 数据库预期设置的恢复时间，来自于参数FAST_START_MTTR_TARGET，单位：秒 |
| ESTIMATED\_MTTR | INTEGER | 执行数据库恢复时预计需要的时间，单位：秒 |
