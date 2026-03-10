本视图显示spin锁的信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| NAME | VARCHAR(32) | spin锁的名称 |
| SPINCOUNT | INTEGER | 每次sleep之前尝试加锁的次数 |
| TIMES | BIGINT | 加锁等待的次数， 通过本字段可以判断spin锁的冲突程度大小 |

