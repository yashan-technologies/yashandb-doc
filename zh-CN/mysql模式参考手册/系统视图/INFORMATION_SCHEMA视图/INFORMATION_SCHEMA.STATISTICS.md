本视图显示索引统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_CATALOG | VARCHAR(3) | 索引所在表的目录名 |
| TABLE\_SCHEMA | VARCHAR(64) | 索引所在表的模式名 |
| TABLE\_NAME | VARCHAR(64) | 索引所在表的名称 |
| NON\_UNIQUE | BIGINT | 索引是否非唯一 |
| INDEX\_SCHEMA | VARCHAR(64) | 索引所属的模式名称 |
| INDEX\_NAME | VARCHAR(64) | 索引的名称 |
| SEQ\_IN\_INDEX | BIGINT | 索引列的顺序 |
| COLUMN\_NAME | VARCHAR(64) | 索引列的名称 |
| COLLATION | VARCHAR(1) | 索引列的排序规则 |
| CARDINALITY | BIGINT | 索引列的基数 |
| SUB\_PART | BIGINT | 索引前缀长度<br>仅语法兼容，无实际功能含义 |
| PACKED | VARCHAR(1) | 打包方式<br>仅语法兼容，无实际功能含义 |
| NULLABLE | VARCHAR(3) | 索引列是否包含空值 |
| INDEX\_TYPE | VARCHAR(8) | 索引类型 |
| COMMENT | VARCHAR(4000) | 注释。仅完成视图兼容，不具有实际含义 |
| INDEX\_COMMENT | VARCHAR(4000) | 索引的注释。仅完成视图兼容，不具有实际含义 |
