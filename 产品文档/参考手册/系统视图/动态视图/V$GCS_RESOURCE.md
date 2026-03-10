本视图显示共享集群数据页面资源情况。

| 字段  | 类型  | 描述  |
| --- | --- | --- |
| RESOURCE_NAME | VARCHAR(128) | 资源名称，block资源\[space\]\[file\]\[id\]  |
| XOWNER | TINYINT | 持有锁或最近一次持有锁的节点 |
| OWNER_COUNT | TINYINT | 持有资源的节点数 |
| OWNER_MAP | BIGINT | 持有资源的节点位图，64位整型值，每一位代表节点的ID，如果该节点持有资源，ownerMap中对应的位设置为1 |
| IN_PROCESS | BOOLEAN | 是否有节点请求获取当前资源 |
| REQUEST_COUNT | TINYINT | 资源上当前请求消息数量 |
| PASTCOPY_MAP | BIGINT | 持有PASTCOPY的节点位图，此字段标记持有该BLOCK的PASTCOPY资源的节点 |
| DISK_LSN | BIGINT | 最近一次刷盘的LSN |
| WRITE_INST | TINYINT | 正在刷盘的实例ID |
| OBJ | BIGINT | 当前资源所属的对象ID |