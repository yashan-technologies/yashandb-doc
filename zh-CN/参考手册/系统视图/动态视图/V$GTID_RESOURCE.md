本视图显示共享集群中的全局事务ID资源情况。

|  字段| 类型| 描述|
| --- | --- | --- |
| RESOURCE\_NAME | VARCHAR(272) | 资源名称，gtid资源\[len\]\[string\]  |
| XOWNER | TINYINT | 持有写锁或最近一次持有写锁的节点 |
| OWNER\_COUNT | TINYINT | 持有资源的节点数 |
| OWNER\_MAP | BIGINT | 持有资源的节点位图，64位整型值，每一位代表节点的ID，如果该节点持有资源，ownerMap中对应的位设置为1 |
| IN\_PROCESS | BOOLEAN | 是否有节点请求获取当前资源 |
| REQUEST\_COUNT | TINYINT | 资源上当前请求消息数量 |
| START\_SESSION\_ID | SMALLINT | 开启该事务的会话ID |
| FINISH\_SESSION\_ID | SMALLINT | 结束该事务的会话ID |
| FINISH\_INSTANCE\_ID | TINYINT | 结束该事务的实例ID |
