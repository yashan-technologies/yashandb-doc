本视图显示系统中所有的归档日志备份集信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| RECID# | BIGINT  |备份集编号 |
| INSTANCE_NUMBER# | INTEGER      | 实例编号 |
| START_TIME | DATE |开始时间，根据内部SCN转换得来，可能与实际机器时间存在误差 |
| COMPLETION_TIME | DATE |完成时间，根据内部SCN转换得来，可能与实际机器时间存在误差 |
| TYPE  | VARCHAR(11) |备份类型<br>\* FULL：全量备份 <br>\* INCREMENTAL：增量备份<br> \* ARCHIVE：归档备份|
| PATH  | VARCHAR(256) | 备份集路径 |
| TAG   | VARCHAR(64) | 唯一标签   |
| COMPRESS_ALGO  | VARCHAR(4) |压缩算法 |
| COMPRESS_LEVEL | VARCHAR(6) | 压缩级别 |
| ENCRYPT_ALGO   | VARCHAR(6) |加密算法 |
| INPUT_BYTES    | BIGINT  | 备份过程中，读取的总字节数 |
| OUTPUT_BYTES   | BIGINT  | 备份过程中，刷盘的总字节数 |
| SEQUENCE_BEGIN# | INTEGER | 备份集中归档文件最小的ASN |
| SEQUENCE_END# | INTEGER  | 备份集中归档文件最大的ASN |
| MIN_FIRST_CHANGE#   | BIGINT | 备份集中归档最小的SCN |
| MAX_NEXT_CHANGE#   | BIGINT  | 备份集中归档最大的SCN |
| RESTORE_TIME   | DATE |当前备份集产生时，数据库最近一次restore的时间，若DB未执行过RESTORE操作，显示为空 |
