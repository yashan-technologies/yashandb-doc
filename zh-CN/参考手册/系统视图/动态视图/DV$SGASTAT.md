本视图显示存算一体分布式集群中所有节点的全局内存各个内存池详细信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| POOL | VARCHAR(32) | 内存池类型          |
| NAME | VARCHAR(32) | 内存池名称          |
| BYTES | BIGINT | 内存池大小（单位：字节）  |
