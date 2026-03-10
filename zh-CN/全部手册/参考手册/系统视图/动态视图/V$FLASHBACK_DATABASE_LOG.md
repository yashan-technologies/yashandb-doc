本视图显示全库闪回功能的相关信息。



存算一体分布式集群部署中无此功能，本视图无意义。



|  字段| 类型| 说明|
| --- | --- | --- |
| OLDEST_FLASHBACK_SCN | BIGINT | 全库闪回能到达的最老时间点对应的scn |
| OLDEST_FLASHBACK_TIME | TIMESTAMP(6) | 全库闪回能到达的最老时间点对应的timestamp |
| RETENTION_TARGET | BIGINT | 全库闪回日志文件的最大保留时长（单位：分钟），保留时长超过该值的日志文件才符合自动清理条件 |
| TOTAL_FILE_SIZE | BIGINT | 当前全库闪回所有日志文件所占空间（单位：字节） |
