本视图显示分布式集群中所有节点会话使用SPA内存信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| TYPE | VARCHAR(32) | 内存类型 |
| USE_SIZE | BIGINT | 内存使用大小（单位：字节） |