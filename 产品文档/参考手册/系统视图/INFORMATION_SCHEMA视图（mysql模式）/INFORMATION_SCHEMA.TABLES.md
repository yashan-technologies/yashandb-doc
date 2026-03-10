本视图显示表信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| TABLE_CATALOG | VARCHAR(3) | 表的目录名 |
| TABLE_SCHEMA | VARCHAR(64) | 表的模式名 |
| TABLE_NAME | VARCHAR(64) | 表的名称 |
| TABLE_TYPE | VARCHAR(10) | 表类型（BASE TABLE、VIEW、SYSTEM VIEW） |
| ENGINE | VARCHAR(8) | 表所属的存储引擎，固定为YashanDB |
| VERSION | INT | 表的版本号， 固定为10 |
| ROW_FORMAT | VARCHAR(7) | 表使用的行格式（COMPACT） |
| TABLE_ROWS | BIGINT | 表中的行数 |
| AVG_ROW_LENGTH | BIGINT | 表每行的平均长度 |
| DATA_LENGTH |  BIGINT | 表中数据所占空间的字节数。当前未统计，固定为0  |
| MAX_DATA_LENGTH | BIGINT | 表中数据允许占用的最大空间（单位：字节）。当前未统计，固定为0  |
| INDEX_LENGTH | BIGINT | 索引所占空间大小。当前未统计，固定为0 |
| DATA_FREE | BIGINT | 表中未分配给任何对象的空间。当前未统计，固定为0  |
| AUTO_INCREMENT | BIGINT UNSIGNED | 表中下一个自动增量值的预期值 |
| CREATE_TIME | TIMESTAMP | 表创建的时间 |
| UPDATE_TIME | DATE | 表更新的时间 |
| CHECK_TIME | DATE | 表最后一次被检查的时间 |
| TABLE_COLLATION | VARCHAR(32) | 表使用的字符集和校对规则（当前设置无效且无法修改）|
| CHECKSUM | BIGINT | 表的校验和 |
| CREATE_OPTIONS | VARCHAR(11) | 表的其他选项，包括加密方式、行格式、是否分区等 |
| TABLE_COMMENT | VARCHAR(4000) | 索引的注释 |
