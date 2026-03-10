本视图显示分布式集群中所有节点spin锁的信息 。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| NAME | VARCHAR(32) | spin锁的名称 |
| SPINCOUNT | INTEGER | 每次sleep之前尝试加锁的次数 |
| TIMES | BIGINT | 加锁等待的次数， 通过本字段可以判断spin锁的冲突程度大小 |
