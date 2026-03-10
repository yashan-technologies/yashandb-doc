本视图显示向量化计算过程中内存池的详细信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SIZE_CLASS | VARCHAR(64) | 管理的单个内存块的大小  |
| FREE_MEMORY | BIGINT | 空闲内存量 |
| USED_MEMORY	 | BIGINT  | 使用中的内存量 |
| MAX_USED_MEMORY | BIGINT | 使用内存量峰值 |
| ALLOC_TIMES | BIGINT | 向内存池成功申请内存块的总次数 |
| DEALLOC_TIMES | BIGINT | 向内存池释放内存块的总次数 |
| SYS_ALLOC_TIMES | BIGINT | 向操作系统成功申请内存块的总次数 |
| SYS_DEALLOC_TIMES | BIGINT | 向操作系统释放内存块的总次数 |