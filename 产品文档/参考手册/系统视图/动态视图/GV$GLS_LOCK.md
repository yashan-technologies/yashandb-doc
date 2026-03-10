本视图显示共享集群中的全局锁情况。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID | BIGINT | 全局锁ID |
| TYPE | VARCHAR(32) | 全局锁TYPE<br>\*   OBJECT_LOCK：对象锁<br>\*   SEGMENT_LOCK：segment锁<br>\*   SEGMENT_EXTEND_LOCK：扩展segment锁<br>\*   INTERVAL_EXTEND_LOCK：interval分区扩展锁<br>\*   USER_LOCK：用户锁<br>\*   SYSTEM_LOCK：系统锁<br>\*   SPC_EXTENT_LOCK：表空间extent锁<br>\*   ROLE_LOCK：角色锁<br>\*   UNKNOWN：未知锁类型 |
| RESOURCE_NAME | VARCHAR(128) | 资源名称 |
| GLOBAL_STATUS | TINYINT | 缓存的MASTER RESOURCE的锁状态，本地释放锁后不会清除MODE<br>\*   0：NONE，未登记<br>\*   1：SHARE，登记为共享锁状态<br>\*   2：EXCLUSIVE，登记为互斥锁状态 |
| LOCAL_STATUS | TINYINT | 本地的锁状态<br>\*   0：IDLE，当前未使用该锁<br>\*   1：SHARE，正在将锁作为共享锁使用<br>\*   2：INTENTIONAL EXCLUSIVE，意向互斥锁<br>\*   3：EXCLUSIVE，正在将锁作为互斥锁使用 |
| SHARE_COUNT | SMALLINT | 共享锁持有者数量 |
| XID | BIGINT | 持有排他锁的XRM XID，此字段用于显示X锁所对应的事务，当LOCAL_STATUS=EXCLUSIVE时，此字段才具备意义，否则为NULL |
