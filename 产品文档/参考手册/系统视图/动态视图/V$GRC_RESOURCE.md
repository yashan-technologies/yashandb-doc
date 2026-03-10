本视图显示共享集群中的全局资源情况。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| RESOURCE_NAME | VARCHAR(272) | 资源名称，block资源\[space\]\[file\]\[id\]，lock资源\[id\]\[type\]，GTID资源\[len\]\[string\]  |
| TYPE | INTEGER | 资源类型<br>\*   0：BLOCK，数据页面<br>\*   1：LOCK，锁资源<br>\*   2：GTID，全局事务ID资源  |
| XOWNER | TINYINT | 持有写锁或最近一次持有写锁的节点 |
| OWNER_COUNT | TINYINT | 持有资源的节点数 |
| OWNER_MAP | BIGINT | 持有资源的节点位图，64位整型值，每一位代表节点的ID，如果该节点持有资源，ownerMap中对应的位设置为1 |
| IN_PROCESS | BOOLEAN | 是否有节点请求获取当前资源 |
| REQUEST_COUNT | TINYINT | 资源上当前请求消息数量 |
