本视图显示具有约束的列信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| CONSTRAINT_CATALOG | VARCHAR(3) | 约束所属目录名称，此值始终为def |
| CONSTRAINT_SCHEMA | VARCHAR(64) | 约束所在的模式名 |
| CONSTRAINT_NAME | VARCHAR(64) | 约束的名称 |
| TABLE_CATALOG | VARCHAR(3) | 约束所在表所属目录名称，此值始终为def |
| TABLE_SCHEMA | VARCHAR(64) | 约束所在表的模式名 | 
| TABLE_NAME | VARCHAR(64) | 约束所在表的名称 |
| COLUMN_NAME | VARCHAR(64) | 具有约束的列的名称<br/>如果约束是外键，该项是外键的列（而非外键引用的列） |
| ORDINAL_POSITION | BIGINT | 约束中列的位置，列位置从1开始编号 |
| POSITION_IN_UNIQUE_CONSTRAINT | BIGINT | 对于唯一和主键约束，该项为NULL<br/>对于外键约束，该项为外键关联约束的列位置 |
| REFERENCED_TABLE_SCHEMA | VARCHAR(64) | 对于唯一和主键约束，该项为NULL<br/>对于外键约束，该项为外键关联约束所在表的模式名 | 
| REFERENCED_TABLE_NAME | VARCHAR(64) | 对于唯一和主键约束，该项为NULL<br/>对于外键约束，该项为外键关联约束所在表的名称 | 
| REFERENCED_COLUMN_NAME | VARCHAR(64) | 对于唯一和主键约束，该项为NULL<br/>对于外键约束，该项为外键关联约束的列名称 | 
