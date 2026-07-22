本视图显示共享集群中的数据页面资源情况。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| RESOURCE\_NAME | VARCHAR(128) | 资源名称，block资源\[space\]\[file\]\[id\] |
| XOWNER | TINYINT | 持有锁或最近一次持有锁的节点 |
| OWNER\_COUNT | TINYINT | 持有资源的节点数 |
| OWNER\_MAP | BIGINT | 持有资源的节点位图，64位整型值，每一位代表节点的ID，如果该节点持有资源，ownerMap中对应的位设置为1 |
| IN\_PROCESS | BOOLEAN | 是否有节点请求获取当前资源 |
| REQUEST\_COUNT | TINYINT | 资源上当前请求消息数量 |
| PASTCOPY\_MAP | BIGINT | 持有PASTCOPY的节点位图，此字段标记持有该BLOCK的PASTCOPY资源的节点 |
| DISK\_LSN | BIGINT | 最近一次刷盘的LSN |
| WRITE\_INST | TINYINT | 正在刷盘的实例ID |
| OBJ | BIGINT | 当前资源所属的对象ID |
| IS_AFFINITY | BOOLEAN | 该资源是否属于实例亲和对象 |
