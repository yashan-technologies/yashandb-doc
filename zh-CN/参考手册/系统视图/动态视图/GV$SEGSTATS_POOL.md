本视图显示数据库中段（segment）的详细统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
|  OBJ# | BIGINT | 对象OID |
| DATAOBJ# | BIGINT | 对象DATAOID |
| TS# | INTEGER | 对象所在表空间ID |
| OBJTYPE | INTEGER | 对象类型 |
| OBJNAME | VARCHAR(64) | 对象名称 |
| SUBOBJNAME | VARCHAR(64) | 对象子名称 |
| TSNAME | VARCHAR(64) | 对象所在表空间名称 |
| OWNERNAME | VARCHAR(64) | 对象所有者名称 |
| LOGIC_READS | BIGINT | 逻辑读块次数 |
| PHYSICS_READS | BIGINT | 物理读块次数 |
| BUFFER_BUSY_WAITS | BIGINT | 缓存等待的次数 |
| XSLOT_WAITS | BIGINT | 事务槽不足造成的等待次数 |
| ROW_LOCK_WAITS | BIGINT | 对象行被其他事务锁住造成的等待次数 |
| PHYSICS_READ_REQUESTS | BIGINT | 物理读请求次数 |
| DB_BLOCK_CHANGES | BIGINT | 构建一致性读过程中回滚的事务数 |
| GC_CR_BLOCKS_RECEIVED | BIGINT | 集群下一致性读块接收块数 |
| GC_CURRENT_BLOCKS_RECEIVED | BIGINT | 集群下当前块接收块数 |
| GC_REMOTE_GRANTS | BIGINT | 集群下远程授权读取磁盘次数 |
| GC_BUFFER_BUSY | BIGINT | 对象集群下缓冲区忙等待的次数 |
| SEGMENT_SCANS | BIGINT | 段扫描的次数 |
| SPACE_ALLOC_SIZE | BIGINT | 段的大小 |
| DELTA_LOGIC_READS | BIGINT | 产生快照后至今逻辑读块次数变化量 |
| DELTA_PHYSICS_READS | BIGINT | 产生快照后至今物理读块次数变化量 |
| DELTA_BUFFER_BUSY_WAITS | BIGINT | 产生快照后至今等待buffer的次数变化量 |
| DELTA_XSLOT_WAITS | BIGINT | 产生快照后至今数据块事务槽不足造成的等待次数变化量 |
| DELTA_ROW_LOCK_WAITS | BIGINT | 产生快照后至今行被其他事务锁住造成的等待次数变化量 |
| DELTA_PHYSICS_READ_REQUESTS | BIGINT | 产生快照后至今物理读请求次数变化量 |
| DELTA_DB_BLOCK_CHANGES | BIGINT | 产生快照后至今构建一致性读过程中回滚的事务数变化量 |
| DELTA_GC_CR_BLOCKS_RECEIVED | BIGINT | 产生快照后至今集群下一致性读块接收块数变化量 |
| DELTA_GC_CURRENT_BLOCKS_RECEIVED | BIGINT | 产生快照后至今集群下当前接收块数变化量 |
| DELTA_GC_REMOTE_GRANTS | BIGINT | 产生快照后至今集群下远程授权读取磁盘次数变化量 |
| DELTA_GC_BUFFER_BUSY | BIGINT | 产生快照后至今集群下buffer busy wait的次数变化量 |
| DELTA_SEGMENT_SCANS | BIGINT | 产生快照后至今段扫描的次数变化量 |
| DELTA_SPACE_ALLOC_SIZE | BIGINT | 产生快照后至今段的大小变化量 |

