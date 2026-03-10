本视图显示最近一次实例恢复任务的信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| IN_RECOVERY | BOOLEAN | 是否处于故障恢复中 |
| INSTANCES | BIGINT | 恢复实例列表，64位整型值，需转为二进制查看，在二进制中每一位表示一个实例的状态（与INST_ID顺序对应），若某个实例故障则其对应的INSTANCES位的值为1，实例正常则为0 |
| BEGIN_TIME | TIMESTAMP | 实例恢复开始的时间 |
| END_TIME | TIMESTAMP | 实例恢复结束的时间 |
| RECOVERY_REDO_SIZE | BIGINT | 实例恢复涉及的redo量（单位：字节） |
| COMPLETE_REDO_SIZE | BIGINT | 当前实例恢复已完成的redo量（单位：字节） |
| GLOBAL_RESOURCE_AVAILABLE_TIME | TIMESTAMP | 全局资源可用时间 |
| COMPLETE_RECOVERY_TIME | TIMESTAMP | 实例恢复完全可用的时间 |
| PHASE | VARCHAR(68) | 当前故障恢复的阶段 |
