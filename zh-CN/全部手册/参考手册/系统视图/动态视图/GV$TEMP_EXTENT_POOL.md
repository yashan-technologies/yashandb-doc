本视图显示数据库会话中全局临时表的segment信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| TABLESPACE_NAME | VARCHAR(64) | 表空间名称 |
| FILE_ID | INTEGER | 数据文件全局ID |
| EXTENTS_CACHED | NUMBER | 已缓存的extent数量 |
| EXTENTS_USED | NUMBER | 已使用的extent数量 |
| BLOCKS_CACHED | NUMBER | 已缓存的block数量 |
| BLOCKS_USED | NUMBER | 已使用的block数量 |
| BYTES_CACHED | NUMBER | 已缓存的字节数 |
| BYTES_USED | NUMBER | 已使用的字节数 |
| INTER_FNO | INTEGER | 数据文件在表空间内的文件ID |
