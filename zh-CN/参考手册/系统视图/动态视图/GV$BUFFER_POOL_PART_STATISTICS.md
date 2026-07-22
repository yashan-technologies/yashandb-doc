本视图显示数据缓存区的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| ID  | INTEGER | 缓存区分区的编号 |
| SIZE | BIGINT | 缓存区分区的大小（单位：字节） |
| NUM\_TOTAL | INTEGER | 数据块总数 |
| NUM\_RESIDENT | INTEGER | 常驻内存数据块数量 |
| NUM\_MAIN | INTEGER | 热块链数据块数量 |
| NUM\_AUXILLIARY | INTEGER | 辅助链数据块数量 |
| NUM\_WRITE | INTEGER | 脏页链数据块数量 |
| NUM\_TEMP | INTEGER | 临时链数据块数量 |
| NAME | VARCHAR(32) | 缓冲区的名称，当前固定为DEFAULT |
| SET\_MSIZE | BIGINT | 缓冲区内可设置的最大的可容纳数据块数量 |
| BLOCK\_SIZE | BIGINT | 缓冲区池子中被管理的数据块的大小 |
| PHYSICAL\_READS | BIGINT | 物理读次数 |
| PHYSICAL\_WRITES | BIGINT | 物理写次数 |
