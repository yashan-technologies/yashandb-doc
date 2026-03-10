单机/共享集群/分布式集群部署中，本视图显示当前实例的数据库汇总信息以及实例ID。

存算一体分布式集群部署中，在CN上本视图显示MN实例的数据库汇总信息，在MN或DN上本视图显示当前实例的数据库汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| DATABASE_ID | BIGINT | 数据库ID |
| DATABASE_NAME | VARCHAR(64) | 数据库名称 |
| CREATE_TIME | DATE | 建库时间 |
| LOG_MODE | VARCHAR(16) | 是否已开启归档<br>\*   ARCHIVELOG：已开启归档<br>\*   NOARCHIVELOG：未开启归档 |
| OPEN_MODE | VARCHAR(16) | 启动模式<br>\*   READ_ONLY：只读模式<br>\*   READ_WRITE：读写模式<br>\*   MOUNTED：mount状态 |
| PROTECTION_MODE | VARCHAR(32) | 保护模式<br>\*   MAXIMUM PERFORMANCE：最大性能<br>\*   MAXIMUM AVAILABILITY：最大可用<br>\*   MAXIMUM PROTECTION：最大保护 |
| PROTECTION_LEVEL | VARCHAR(32) | 保护级别<br>\*   UNPROTECTED：数据库没有OPEN<br>\*   MAXIMUM PERFORMANCE：最大性能生效中<br>\*   MAXIMUM AVAILABILITY：最大可用生效中，并且redo已经同步到备库<br>\*   RESYNCHRONIZATION：最大可用生效中，但是redo没有同步到备库<br>\*   MAXIMUM PROTECTION：最大保护生效中，且redo同步正常<br>\*   SYNCHRONIZATION BLOCKING：最大保护生效中，但是redo同步不正常，事务提交将阻塞 |
| DATABASE_ROLE | VARCHAR(16) | 数据库角色<br>\*   PRIMARY：主库<br>\*   STANDBY：物理备库<br>\*   LOGICAL STANDBY：逻辑备库 |
| BLOCK_SIZE | INTEGER | 数据库数据块大小（单位：字节） |
| CURRENT_SCN | BIGINT | 数据库当前SCN |
| STATUS | VARCHAR(32) | 数据库状态<br>\*   NORMAL：正常<br>\*   NEED REPAIR：备库状态异常，需要重新build<br>\*   REDO MISMATCH：备库有部分日志和主库不匹配，需要检查和修复<br/>\*   ABNORMAL：异常状态，不能执行写操作 |
| RCY_POINT | VARCHAR(32) | 数据库启动时的日志恢复点，格式为{resetid}-{asn}-{blockid}-{lfn}  |
| FLUSH_POINT | VARCHAR(32) | 数据库当前日志刷盘点，格式为{resetid}-{asn}-{blockid}-{lfn}   |
| RESET_POINT | VARCHAR(32) | 数据库日志重置点，格式为{resetid}-{asn}-{blockid}-{lfn}   |
| PLATFORM_NAME | VARCHAR(256) | 操作系统 |
| HOST_NAME | VARCHAR(256) | 主库用户名 |
| RESTORE_TIME | TIMESTAMP(6) | 数据库restore的时间戳 |
| SWITCHOVER_STATUS | VARCHAR(32) | 主备角色切换的状态<br>\*   NOT ALLOWED：切换条件不满足<br>\*   TO STANDBY：数据库已准备好切换到备角色<br>\*   TO PRIMARY：数据库已准备好切换到主角色<br>\*   WAIT PRIMARY DEMOTE：正在切换中，等待主库完成降备<br>\*   PROMOTING：备库正在切换为主库<br>\*   DEMOTING：主库正在切换为备库  |
| SUPPLEMENTAL_LOG_DATA_MIN | VARCHAR(8) | 是否开启最小附加日志<br>\*   YES：开启<br>\*   NO：关闭 <br>\*   IMPLICIT：隐式开启 |
| SUPPLEMENTAL_LOG_DATA_PK | VARCHAR(8) | 是否开启PRIMARY KEY模式附加日志<br>\*   YES：开启<br>\*   NO：关闭 |
| SUPPLEMENTAL_LOG_DATA_ALL | VARCHAR(8) | 是否开启ALL模式附加日志<br>\*   YES：开启<br>\*   NO：关闭 |
| SUPPLEMENTAL_LOG_TABLE_TYPE | VARCHAR(16) | 数据库级DML附加日志生效的表类型，取值范围为[HEAP，TAC，LSC]，为空表示所有表DML都不记录附加日志 |
| GUARD_STATUS | VARCHAR(8) | 保护数据不被更改<br>\*   ALL：表示除SYS之外的所有用户无法更改数据库中的任何数据<br>\*   NONE：表示不拦截对数据的更改。 |
| DBID | BIGINT | 数据库ID |
| NAME | VARCHAR(64) | 数据库名称 |
| CREATED | DATE | 建库时间 |
| RESETLOGS_CHANGE# | BIGINT | open resetlogs时的系统修改序列号（SCN） |
| FLASHBACK_ON | VARCHAR(8) | 是否开启全库闪回功能<br/>*  YES：开启<br/>*  NO：关闭 |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid：redo日志的reset id，每次重置redo时间线会使该值加1。
> - asn：归档序列号（Archive Sequence Number），每产生一个redo，ASN会加1，每个redo的ASN不相同。
> - blockid：redo文件内页面所在ID，页面的偏移量为block id \* block size。
> - lfn：日志序列号（Log Flush Number），每次redo刷盘，LFN加1。

