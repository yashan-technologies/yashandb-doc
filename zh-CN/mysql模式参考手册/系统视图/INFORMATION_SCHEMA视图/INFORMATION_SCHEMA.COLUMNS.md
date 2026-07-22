本视图显示列信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_CATALOG | VARCHAR(3) | 列所在表的目录名 |
| TABLE\_SCHEMA | VARCHAR(64) | 列所在表的模式名 |
| TABLE\_NAME | VARCHAR(64) | 列所在表的名称 |
| COLUMN\_NAME | VARCHAR(64) | 列的名称 |
| ORDINAL\_POSITION | BIGINT | 列在表中的位置 |
| COLUMN\_DEFAULT | VARCHAR(8000) | 列的默认值 |
| IS\_NULLABLE | VARCHAR(3) | 列是否允许为空 |
| DATA\_TYPE | CLOB | 列的数据类型 |
| CHARACTER\_MAXIMUM\_LENGTH | BIGINT | 字符类型列的最大长度 |
| CHARACTER\_OCTET\_LENGTH | BIGINT | 字符类型列中存储的字符所占的字节数 |
| NUMERIC\_PRECISION | BIGINT | 数值列中允许的最大位数 |
| NUMERIC\_SCALE | BIGINT | 数值数据类型的小数部分的位数 |
| DATETIME\_PRECISION | BIGINT | 日期时间数据类型的小数部分的位数 |
| CHARACTER\_SET\_NAME | VARCHAR(32) | 字符集名称（当前设置无效且无法修改）|
| COLLATION\_NAME | VARCHAR(32) | 排序规则名称（当前设置无效且无法修改）|
| COLUMN\_TYPE | CLOB | 列数据类型 |
| COLUMN\_KEY | VARCHAR(12) | 列是否为表的主键或唯一索引的一部分 |
| EXTRA | VARCHAR(120) | 列的附加属性 |
| PRIVILEGES | VARCHAR(1) | 列上拥有的权限<br>仅语法兼容，无实际功能含义 |
| COLUMN\_COMMENT | VARCHAR(4096) | 列的注释或描述 |
| GENERATION\_EXPRESSION | VARCHAR(1) | 存储生成列的计算表达式<br>仅语法兼容，无实际功能含义 |
