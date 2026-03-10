本视图显示分布式集群中所有节点的未决事务相关信息及其状态。

| 字段                      | 类型  | 说明                                                                                                                                              |
|-------------------------| --- |-------------------------------------------------------------------------------------------------------------------------------------------------|
| GROUP_ID               | INTEGER | 组ID                                                                                                                                             |
| GROUP_NODE_ID         | INTEGER | 组内节点ID                                                                                                                                          |
| GLOBAL_TRAN_ID        | BIGINT | XA事务的GTID（全局事务号）                                                                                                                                |
| LOCAL_SESSION_ID      | INTEGER | XA事务所属本地会话ID                                                                                                                                    |
| GLOBAL_SESSION_ID     | INTEGER | XA事务所属分布式会话ID                                                                                                                                   |
| SESSION_SERIAL         | INTEGER | XA事务所属分布式会话的版本号                                                                                                                                 |
| STATE                   | VARCHAR(16) | XA事务状态<br>\*   IDLE<br>\*   OPEN<br>\*   PREPARED<br>\*   COMMIT<br>\*   ROLLBACKING<br>\*   ROLLBACK<br>\*   COMMIT FORCE<br>\*   ROLLBACK FORCE |
| SCN                     | BIGINT | 未决事务的结束SCN                                                                                                                                      |
| TRAN_COMMENT           | VARCHAR(256) | 事务备注                                                                                                                                            |
| PENDING_TIME           | DATE | XA事务进入未决状态的时间；若数据库重启，此字段失效                                                                                                                      |
| RETRY_TIME             | DATE | 数据库重启后，处于phase1的XA事务重新进入未决状态的时间                                                                                                                 |
| NODE_LIST               | VARCHAR(8000) | 分布式事务节点信息                                                                                                                                       |
| IS_DSTB_SESSION_UNBIND  | VARCHAR(8) | 分布式事务是否已经与分布式会话解绑                                                                                                                                 |

