本视图显示触发器的信息。

|  字段| 类型| 说明|
|----------------------------|-------------|--------------------------------------------------------------|
| TRIGGER_CATALOG            | VARCHAR(3)     | trigger所属目录名称，此值始终为def                                       |
| TRIGGER_SCHEMA             | VARCHAR(64) | trigger所属schema名称                                            |
| TRIGGER_NAME               | VARCHAR(64) | trigger名称                                                    |
| EVENT_MANIPULATION         | VARCHAR(26) | trigger事件，值可以是INSERT、DELETE、UPDATE                           |
| EVENT_OBJECT_CATALOG       | VARCHAR(3)     | 与trigger关联的表的所属目录，此值始终为def                                   |
| EVENT_OBJECT_SCHEMA        | VARCHAR(64) | 与trigger关联的表的schema                                          |
| EVENT_OBJECT_TABLE         | VARCHAR(64) | 与trigger关联的表的表名                                              |
| ACTION_ORDER               | VARCHAR(20)    | trigger操作在同一个表上具有相同EVENT_MANIPULATION和ACTION_TIMING值的触发器列表中的序号位置<br>仅语法兼容，无实际功能含义|
| ACTION_CONDITION           | VARCHAR(4)     | 此值始终为NULL                                                    |
| ACTION_STATEMENT           | LONGTEXT    | trigger主体；即触发器激活时执行的语句                                       |
| ACTION_ORIENTATION         | VARCHAR(3)     | 此值始终为ROW                                                     |
| ACTION_TIMING              | VARCHAR(6)  | trigger激活的时机<br/>* BEFORE：在触发事件之前<br/>* AFTER：在触发事件之后        |
| ACTION_REFERENCE_OLD_TABLE | VARCHAR(4)     | 此值始终为NULL                                                    |
| ACTION_REFERENCE_NEW_TABLE | VARCHAR(4)     | 此值始终为NULL                                                    |
| ACTION_REFERENCE_OLD_ROW   | VARCHAR(3)     | 旧的列标识符，此值始终为OLD                                              |
| ACTION_REFERENCE_NEW_ROW   | VARCHAR(3)     | 新的列标识符，此值始终为NEW                                              |
| CREATED                    | VARCHAR(20)    | trigger的创建日期和时间<br>仅语法兼容，无实际功能含义                                              |
| SQL_MODE                   | VARCHAR(20)    | trigger创建时生效的SQL_MODE，触发器将在该模式下执行<br>仅语法兼容，无实际功能含义                            |
| DEFINER                    | VARCHAR(64) | 子句中命名的账户DEFINER（通常是创建trigger的数据库实例）  |
| CHARACTER_SET_CLIENT       | VARCHAR(20)    | 创建trigger时系统变量CHARACTER_SET_CLIENT的会话值<br>仅语法兼容，无实际功能含义                       |
| COLLATION_CONNECTION       | VARCHAR(20)    | 创建trigger时系统变量COLLATION_CONNECTION的会话值<br>仅语法兼容，无实际功能含义                       |
| DATABASE_COLLATION         | VARCHAR(20)    | 与trigger关联的数据库的排序规则<br>仅语法兼容，无实际功能含义                               |
