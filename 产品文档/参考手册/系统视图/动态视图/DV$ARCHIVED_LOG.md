本视图显示分布式集群中所有节点归档文件统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| NAME | VARCHAR(255) | 归档文件路径 |
| SEQUENCE# | INTEGER | 归档文件序列号 |
| THREAD# | TINYINT | 实例编号 |
| RESETLOGS_ID | INTEGER | 归档文件reset ID |
| FIRST_CHANGE# | BIGINT | 归档文件内，第一条日志的SCN |
| FIRST_TIME | TIMESTAMP | 归档文件内，第一条日志的SCN对应的时间 |
| NEXT_CHANGE# | BIGINT | 归档文件内，最后一条日志的SCN |
| NEXT_TIME | TIMESTAMP | 归档文件内，最后一条日志的SCN对应的时间 |
| BLOCKS | BIGINT | 归档文件的页面数量 |
| BLOCK_SIZE | INTEGER | 归档文件的页面大小（单位：字节） |
| COMPLETION_TIME | DATE | 归档文件的生成时间 |
| COMPRESSED | VARCHAR(8) | 是否是压缩文件 |
| FAL | VARCHAR(8) | 归档日志是否由FAL线程生成 |
| REGISTRAR | VARCHAR(8) | 归档的注册方式。<br>\* MANUAL: 手动注册<br>\* ARCH: 归档线程注册 |