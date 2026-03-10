本视图显示分布式集群中所有节点的数据文件汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| ID  | INTEGER | 数据文件的ID号 |
| NAME | VARCHAR(255) | 数据文件的名称 |
| CREATION_TIME | TIMESTAMP | 数据文件创建时间 |
| TS# | INTEGER | 数据文件所属表空间的ID号 |
| BLOCKS | INTEGER | 数据文件的块数量 |
| BLOCK_SIZE | INTEGER | 数据文件的数据块大小（单位：字节） |
| BYTES | BIGINT | 数据文件大小（单位：字节） |
| STATUS | VARCHAR(8) | 数据文件有online和offline两种状态，offline的数据文件不可用 |
| RELATIVE_FNO | INTEGER | 数据文件在表空间内的ID号 |
| AUTO_EXTEND | VARCHAR(8) | 数据文件是否自动扩展 |
| NEXT_SIZE | BIGINT | 数据文件每次扩展的大小（单位：字节） |
| MAX_SIZE | BIGINT | 数据文件最大可扩至的大小（单位：字节） |
| FREE_BLOCKS | INTEGER | 数据文件内空闲可用的数据块数量 |
| DISK_BYTES | BIGINT | 物理磁盘的占用大小（单位：字节） |
| SHADOW | VARCHAR(8) | 目标临时文件是否为影子状态，TRUE表示为影子文件，FALSE表示为正常文件<br/>若备库升主后，因临时文件为影子状态无法使用，需手动添加新的临时文件供使用<br/>如需通过DROP TEMPFILE删除影子文件，需确保创建该临时文件的数据库为当前主库（若不是，需先切换为主库）并在该主库上执行DROP TEMPFILE语句，其他备库收到DROP TEMPFILE的redo后才会清理影子记录 |
