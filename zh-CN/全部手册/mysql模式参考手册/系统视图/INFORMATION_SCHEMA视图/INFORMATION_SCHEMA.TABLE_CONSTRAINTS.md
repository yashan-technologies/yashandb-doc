本视图显示表的约束信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| CONSTRAINT_CATALOG | VARCHAR(3) | 约束所属目录名称，此值始终为def |
| CONSTRAINT_SCHEMA | VARCHAR(64) | 约束所在的模式名 |
| CONSTRAINT_NAME | VARCHAR(64) | 约束的名称 |
| TABLE_SCHEMA | VARCHAR(64) | 约束所在表所在的模式名 | 
| TABLE_NAME | VARCHAR(64) | 约束所在表的名称 |
| CONSTRAINT_TYPE | VARCHAR(11) | 约束的类型（PRIMARY KEY、UNIQUE、FOREIGN KEY、CHECK） |
