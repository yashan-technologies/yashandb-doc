本视图显示所有表空间下数据文件的空闲空间信息。

- 若某个数据文件（或表空间）完全离线，本视图将不会记录该数据文件（或表空间）的信息。
- 若某个数据库对象既在表空间的在线文件中占用部分EXTENT又在已离线的文件中占用部分EXTENT，本视图将额外记录该部分离线文件的空闲EXTENT信息。
- 若某个数据文件已完全写满（无可用空间），本视图将不会记录该数据文件的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLESPACE\_NAME  | VARCHAR(64) | 表空间名称 |
| FILE\_ID | INTEGER | 数据文件全局ID |
| BLOCK\_ID | INTEGER | 空闲EXTENT中起始block编号 |
| BYTES  | BIGINT |  空闲EXTENT的大小（单位：字节） |
| BLOCKS | INTEGER | 空闲EXTENT的大小（单位：页面） |
| RELATIVE\_FNO | INTEGER | 数据文件在表空间内的序号 |
