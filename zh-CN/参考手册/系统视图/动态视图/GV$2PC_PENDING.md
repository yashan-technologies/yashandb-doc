本视图显示未决事务相关信息及其状态。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID      | NUMBER     | 组ID |
| GROUP_NODE_ID | NUMBER     | 组内节点ID |
| INST_ID       | NUMBER      | 实例ID|
| GLOBAL\_TRAN\_ID | RAW(128) | XA事务的GTID（全局事务号） |
| LOCAL\_SESSION\_ID  | INTEGER | XA事务所属本地会话ID |
| GLOBAL\_SESSION\_ID | INTEGER | XA事务所属分布式会话ID |
| SESSION\_SERIAL | INTEGER | XA事务所属分布式会话的版本号 |
| STATE | VARCHAR(16) | XA事务状态<br>\*   IDLE<br>\*   OPEN<br>\*   PREPARED<br>\*   COMMIT<br>\*   ROLLBACKING<br>\*   ROLLBACK<br>\*   COMMIT FORCE<br>\*   ROLLBACK FORCE |
| SCN | BIGINT | 未决事务的结束SCN |
| TRAN\_COMMENT | VARCHAR(256) | 事务备注 |
| PENDING\_TIME | DATE | XA事务进入未决状态的时间；若数据库重启，此字段失效 |
| RETRY\_TIME | DATE | 数据库重启后，处于phase1的XA事务重新进入未决的时间 |
| NODE\_LIST | VARCHAR(8000) | 分布式事务节点信息 |
