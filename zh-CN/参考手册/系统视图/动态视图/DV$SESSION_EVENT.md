本视图显示存算一体分布式集群中所有节点系统事件统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| GLOBAL\_SESSION\_ID | INTEGER | 分布式下全局会话ID |
| EVENT | VARCHAR(32) | 等待事件名称 |
| TOTAL\_WAITS | BIGINT | 总等待次数 |
| TOTAL\_TIMEOUTS | BIGINT | 总超时次数 |
| TIME\_WAITED | BIGINT | 等待时间（单位：毫秒） |
| AVERAGE\_WAIT | NUMBER | 平均等待时间（单位：毫秒） |
| TIME\_WAITED\_MICRO | BIGINT | 等待时间（单位：微秒） |
| TOTAL\_WAITS\_FG | BIGINT | 前台等待次数 |
| TOTAL\_TIMEOUTS\_FG | BIGINT | 前台超时次数 |
| TIME\_WAITED\_FG | BIGINT | 前台等待时间 （单位：毫秒） |
| AVERAGE\_WAIT\_FG | NUMBER | 前台平均等待时间（单位：毫秒） |
| TIME\_WAITED\_MICRO\_FG | BIGINT | 前台等待时间（单位：微秒） |
| EVENT\_ID | INTEGER | 等待事件ID |
| WAIT\_CLASS | VARCHAR(16) | 等待事件类别<br>\*   APPLICATION：由应用产生的等待，例如不同应用层事务产生的行锁等待<br>\*   CONCURRENCY：数据库内部资源产生的等待，例如latch的竞争等待。<br>\*   COMMIT：事务提交时，redo日志同步产生的等待<br>\*   USER I/O：用户线程产生的I/O，例如读取数据块产生的I/O等待<br>\*   SYSTEM I/O：数据库后台线程I/O导致的等待，例如Database Writer线程将脏数据块同步到磁盘导致的I/O等待<br>\*   OTHER：其他类型的等待<br>\*   IDLE：数据库等待客户端消息，此类等待事件表示会话此时处于空闲状态<br>\*   NETWORK：因网络传输产生的等待，例如数据在网络传输期间产生的等待<br>\*   CONFIGURATION：因数据库实例配置产生的等待，例如配置redo大小、undo大小、DATA BUFFER大小时产生的等待<br>\*   CLUSTER：集群等待事件，例如跨节点交互等待应答<br>\*   DISTRIBUTED：分布式等待事件，例如CN等待各节点回应 |
