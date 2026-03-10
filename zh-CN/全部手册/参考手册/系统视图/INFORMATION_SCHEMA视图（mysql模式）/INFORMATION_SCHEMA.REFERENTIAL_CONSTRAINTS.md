本视图显示外键约束的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| CONSTRAINT_CATALOG | VARCHAR(3) | 外键约束所属目录名称，此值始终为def |
| CONSTRAINT_SCHEMA | VARCHAR(64) | 外键约束所在的模式名 |
| CONSTRAINT_NAME | VARCHAR(64) | 外键约束的名称 |
| UNIQUE_CONSTRAINT_CATALOG | VARCHAR(3) | 外键关联约束所属目录名称，此值始终为def |
| UNIQUE_CONSTRAINT_SCHEMA | VARCHAR(64) | 外键关联约束所在的模式名 |
| UNIQUE_CONSTRAINT_NAME | VARCHAR(64) | 外键外联约束的名称 |
| MATCH_OPTION | VARCHAR(4) | 外键约束属性的值，此值的唯一有效值为NONE |
| UPDATE_RULE | VARCHAR(9) | 外键约束级联更新规则（RESTRICT、CASCADE、SET NULL） |
| DELETE_RULE | VARCHAR(9) | 外键约束级联删除规则（RESTRICT、CASCADE、SET NULL） |
| TABLE_NAME | VARCHAR(64) | 外键约束所在表的名称 |
| REFERENCED_TABLE_NAME | VARCHAR(64) | 外键关联约束所在表的名称 |
