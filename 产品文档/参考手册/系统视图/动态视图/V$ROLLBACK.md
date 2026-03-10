| 字段  | 类型  | 说明  |
| --- | --- | --- |
| XID | BIGINT | 事务的全局ID |
| SID | INTEGER | 该事务绑定的会话ID |
| XRMID | INTEGER | 该事务的XRM ID |
| IN_PRIORITY | VARCHAR(8) | 该事务是否处在优先回滚队列 |
| SORT_POS | INTEGER | 该事务在回滚队列中的位置 |
| RB_POS | INTEGER | 当前回滚队列回滚到的位置 |
| TABLE_LOCK_COUNT | INTEGER | 该事务持有的表锁数量 |
| ROW_LOCK_COUNT | INTEGER | 该事务持有的行锁数量 |
| KEY_LOCK_COUNT | INTEGER | 该事务持有的key锁数量 |