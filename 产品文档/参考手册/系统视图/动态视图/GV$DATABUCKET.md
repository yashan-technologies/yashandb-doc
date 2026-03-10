本视图显示所有LSC表空间的databucket（数据桶）文件信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID | INTEGER | databucket ID |
| NAME | VARCHAR(255) | databucket名称 |
| TYPE | INTEGER | databucket类型<br>\* 0：LOCAL_FS类型 |
| TS# | INTEGER | databucket所属表空间的ID号 |
| SLOT | INTEGER | databucket在所属表空间内的序号 |
| URL | VARCHAR(255) | databucket存储路径 |
| USED_SIZE | BIGINT | databucket已使用空间（单位：字节） |
| MAX_SIZE | BIGINT | databucket最大可用空间（单位：字节） |
| READONLY | BOOLEAN | databucket是否只读 |