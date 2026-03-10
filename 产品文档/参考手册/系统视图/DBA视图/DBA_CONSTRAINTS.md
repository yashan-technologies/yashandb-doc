本视图显示所有的约束信息。

| 字段  | 类型            | 说明  |
| --- |---------------| --- |
| OWNER | VARCHAR(64)   | 约束所属用户名 |
| CONSTRAINT_NAME | VARCHAR(64)   | 约束名 |
| CONSTRAINT_TYPE | VARCHAR(6)    | 约束类型<br/>\* P 主键约束<br/>\* U 唯一约束<br/>\* C check约束<br/>\* R 外键约束<br/>* O 视图的with read only约束<br/>* V 视图的with check option约束 |
| TABLE_NAME | VARCHAR(64)   | 表名  |
| SEARCH_CONDITION | VARCHAR(4000) | check约束的检查条件文本 |
| INDEX_NAME | VARCHAR(64)   | 主键或者唯一约束的索引名 |
| INDEX_OWNER | VARCHAR(64)   | 主键或者唯一约束的索引所属用户名 |
| R_OWNER | VARCHAR(64)   | 外键关联约束所属用户名 |
| R_CONSTRAINT_NAME | VARCHAR(64)   | 外键关联约束名 |
| DELETE_RULE | VARCHAR(9)    | 外键级联删除规则<br>\*   NO ACTION：不做任何操作<br>\*   CASCADE：参照本值的数据一并删除 <br>\*   SET NULL：参照本值的数据的对应列赋空 |
| UPDATE_RULE | VARCHAR(9)    | 外键级联更新规则<br/>\*   NO ACTION：不做任何操作<br/>\*   CASCADE：参照本值的数据一并更新 <br/>\*   SET NULL：参照本值的数据的对应列赋空 |
| STATUS | VARCHAR(8)    | 约束状态<br/>*   ENABLED<br/>*   DISABLED |
| DEFERRABLE | CHAR(14)      | 是否延迟检查状态<br/>*   DEFERRABLE<br/>*   NOT DEFERRABLE |
| DEFERRED | CHAR(9)       | 检查状态<br/>*   DEFERRED<br/>*   IMMEDIATE |
| VALIDATED | VARCHAR(13)   | 约束检查状态<br/>*   VALIDATED<br/>*   NOT VALIDATED |
| GENERATED | VARCHAR(14)   | 约束名称规则<br/>* GENERATED NAME：系统生成名称<br/>* USER NAME：用户指定名称 |
| DATABASE_MAINTAINED | VARCHAR(1)    | 是否DATABASE MAINTAINED<br>\*   Y<br>\*   N |