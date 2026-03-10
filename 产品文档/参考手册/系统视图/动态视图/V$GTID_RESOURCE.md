本视图显示共享集群中的全局事务ID资源情况。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| RESOURCE_NAME | VARCHAR(272) | 资源名称，GTID资源\[len\]\[string\]  |
| XOWNER | TINYINT | 持有写锁或最近一次持有写锁的节点 |
| OWNER_COUNT | TINYINT | 持有资源的节点数 |
| OWNER_MAP | BIGINT | 持有资源的节点位图，64位整型值，每一位代表节点的ID，如果该节点持有资源，ownerMap中对应的位设置为1 |
| IN_PROCESS | BOOLEAN | 是否有节点请求获取当前资源 |
| REQUEST_COUNT | TINYINT | 资源上当前请求消息数量 |
| START_SESSION_ID | SMALLINT | 开启该事务的会话ID |
| FINISH_SESSION_ID | SMALLINT | 结束该事务的会话ID |
| FINISH_INSTANCE_ID | TINYINT | 结束该事务的实例ID |