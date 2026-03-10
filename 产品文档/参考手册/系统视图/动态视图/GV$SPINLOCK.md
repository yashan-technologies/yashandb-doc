本视图显示spin锁的信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| NAME | VARCHAR(32) | spin锁的名称 |
| SPINCOUNT | INTEGER | 每次sleep之前尝试加锁的次数 |
| TIMES | BIGINT | 加锁等待的次数， 通过本字段可以判断spin锁的冲突程度大小 |
