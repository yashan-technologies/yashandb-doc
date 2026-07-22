本视图显示存算一体分布式集群中所有节点的所有LSC表空间的databucket（数据桶）文件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| ID | INTEGER | databucket ID |
| NAME | VARCHAR(255) | databucket名称 |
| TYPE | INTEGER | databucket类型<br>\* 0：LOCAL_FS类型 |
| TS# | INTEGER | databucket所属表空间的ID号 |
| SLOT | INTEGER | databucket在所属表空间内的序号 |
| URL | VARCHAR(255) | databucket存储路径 |
| USED\_SIZE | BIGINT | databucket已使用空间（单位：字节） |
| MAX\_SIZE | BIGINT | databucket最大可用空间（单位：字节） |
| READONLY | BOOLEAN | databucket是否只读 |
