本视图显示向量化计算过程中内存池的详细信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| SIZE\_CLASS | VARCHAR(64) | 管理的单个内存块的大小  |
| FREE\_MEMORY | BIGINT | 空闲内存量 |
| USED\_MEMORY	 | BIGINT  | 使用中的内存量 |
| MAX\_USED\_MEMORY | BIGINT | 使用内存量峰值 |
| ALLOC\_TIMES | BIGINT | 向内存池成功申请内存块的总次数 |
| DEALLOC\_TIMES | BIGINT | 向内存池释放内存块的总次数 |
| SYS\_ALLOC\_TIMES | BIGINT | 向操作系统成功申请内存块的总次数 |
| SYS\_DEALLOC\_TIMES | BIGINT | 向操作系统释放内存块的总次数 |
