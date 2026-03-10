本视图用于检测临时属性的表空间，其extent分配情况。
|  字段| 类型| 说明|
| --- | --- | --- |
| TABLESPACE_NAME | VARCHAR(64) | 表空间名称 |
| FILE_ID | INTEGER | 数据文件全局ID |
| EXTENTS_CACHED | NUMBER | 已缓存的extent数量 |
| EXTENTS_USED | NUMBER | 已使用的extent数量 |
| BLOCKS_CACHED | NUMBER | 已缓存的block数量 |
| BLOCKS_USED | NUMBER | 已使用的block数量 |
| BYTES_CACHED | NUMBER | 已缓存的字节数 |
| BYTES_USED | NUMBER | 已使用的字节数 |
| INTER_FNO | INTEGER | 数据文件在表空间内的文件ID |
