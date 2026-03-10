本视图显示全库闪回功能的相关信息。

仅适用于单机部署，其他部署形态中本视图无意义。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| OLDEST_FLASHBACK_SCN | BIGINT | 全库闪回能到达的最老时间点对应的scn |
| OLDEST_FLASHBACK_TIME | TIMESTAMP | 全库闪回能到达的最老时间点对应的timestamp |
| RETENTION_TARGET | BIGINT | 全库闪回日志文件的最大保留时长（单位：分钟），保留时长超过该值的日志文件才符合自动清理条件 |
| TOTAL_FILE_SIZE | BIGINT | 当前全库闪回所有日志文件所占空间（单位：字节） |
