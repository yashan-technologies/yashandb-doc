本视图显示所有表空间下数据文件的空闲空间信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| TABLESPACE_NAME  | VARCHAR(64) | 表空间名称 |
| FILE_ID | INTEGER | 数据文件全局ID |
| BLOCK_ID | INTEGER | 空闲EXTENT中起始block编号 |
| BYTES  | BIGINT |  空闲EXTENT的大小（单位：字节） |
| BLOCKS | INTEGER | 空闲EXTENT的大小（单位：页面） |
| RELATIVE_FNO | INTEGER | 数据文件在表空间内的序号 |