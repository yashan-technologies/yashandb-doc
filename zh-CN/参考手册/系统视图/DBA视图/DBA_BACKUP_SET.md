本视图显示系统中所有的备份集信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| RECID# | BIGINT  |备份集编号 |
| START\_TIME | DATE |开始时间，根据内部SCN转换得来，可能与实际机器时间存在误差 |
| COMPLETION\_TIME | DATE |完成时间，根据内部SCN转换得来，可能与实际机器时间存在误差 |
| TYPE  | VARCHAR(11) |备份类型<br>\* FULL：全量备份 <br>\* INCREMENTAL：增量备份 <br>\* ARCHIVE：归档备份 <br>\* TABLESPACE：表空间备份 |
| INCREMENT\_LEVEL | INTEGER | 增量备份级的级别<br>\* 0：全部数据都备份 <br>\* 1：表示在某个增量备份集的基础上，只对增量数据做备份 |
| INCREMENT\_ID#   | INTEGER | 增量备份集的组内序号 |
| PATH  | VARCHAR(256) | 备份集路径 |
| TAG   | VARCHAR(64) | 唯一标签   |
| TRUNC\_LSN      | BIGINT  | 备份集基线日志序列号，该序列号之前的redo日志不需要回放 |
| COMPRESS\_LEVEL | VARCHAR(6) | 压缩级别 |
| INPUT\_BYTES    | BIGINT  | 备份过程中，读取的总字节数 |
| OUTPUT\_BYTES   | BIGINT  | 备份过程中，刷盘的总字节数 |
| RCY\_BEGIN\_ASN | INTEGER | 备份集回放开始点对应的ASN：归档序列号，集群下为所有节点中最小的ASN |
| RCY\_BEGIN\_LFN | BIGINT  | 备份集回放开始点对应的LFN：日志序列号 |
| RCY\_END\_ASN   | INTEGER | 备份集回放结束点对应的ASN：归档序列号，集群下为所有节点中最大的ASN|
| RCY\_END\_LFN   | BIGINT  | 备份集回放结束点对应的LFN：日志序列号 |
| COMPRESS\_ALGO  | VARCHAR(4) |压缩算法 |
| ENCRYPT\_ALGO   | VARCHAR(6) |加密算法 |
| DEFAULT\_BASE   | BOOLEAN |默认基线，只针对增量备份 |
| RESTORE\_TIME   | DATE |当前备份集产生时，数据库最近一次restore的时间，若DB未执行过RESTORE操作，显示为空 |
| CHECKPOINT\_TIME   | TIMESTAMP(6) |数据库一致性恢复的时间点，若其显示为空，则该数据库为旧版本升级至新版本，无需参考该项内容 |
| CHECKPOINT\_SCN   | BIGINT |数据库一致性恢复的SCN |
| SPC\_IMPORT\_SCN   | BIGINT | 当前节点中若存在表空间迁移创建的表空间，该SCN更新为所有新迁移表空间的最大SCN |
| SPC\_IMPORT\_LSN   | BIGINT | 当前节点中若存在表空间迁移创建的表空间，该LSN更新为所有新迁移表空间的最大LSN |
| BS\_KEY  | BIGINT | 当前备份集的唯一身份标识 |
| BS\_STATUS| VARCHAR(10) | 当前备份集的状态。<br>\* AVAILABLE：当前备份集可用 <br>\* INCOMPLETE：当前备份集不完整（多租户部署下执行备份时，有部分PDB未完成备份） |
| CON\_ID | BIGINT |该备份集所属的容器ID |
| CON\_UID | VARCHAR(64) | 该备份集所属容器的全局唯一标识|
