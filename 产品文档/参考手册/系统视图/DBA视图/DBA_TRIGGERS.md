本视图显示所有触发器信息。

| 字段  | 类型         | 说明                                                                                                                       |
| --- |------------|--------------------------------------------------------------------------------------------------------------------------|
| OWNER | VARCHAR(64) | 触发器所属用户名                                                                                                                 |
| TRIGGER_NAME | VARCHAR(64) | 触发器名称                                                                                                                    |
| TRIGGER_TYPE | VARCHAR(16) | 触发器的触发时机<br> * BEFORE STATEMENT：语句执行前<br> * BEFORE EACH ROW：每行执行前<br> * AFTER STATEMENT：语句执行后<br> * AFTER EACH ROW：每行执行后 |
| TRIGGERING_EVENT | VARCHAR(26) | 触发器的触发事件                                                                                                                 |
| TABLE_OWNER | VARCHAR(64) | 触发器所在对象（即某张表）的所属用户名                                                                                                             |
| BASE_OBJECT_TYPE | VARCHAR(9) | 触发器所在对象的类型，目前固定为`TABLE`                                                                                                               |
| TABLE_NAME | VARCHAR(64) | 当触发器的基础对象是表时，显示其名称                                                                                                       |
| COLUMN_NAME | VARCHAR(1) | 保留字段，值为NULL                                                                                                              |
| REFERENCING_NAMES | VARCHAR(283) | 触发器内OLD和NEW的相关性名称                                                                                                        |
| WHEN_CLAUSE | VARCHAR(4000) | 触发器的触发条件，为true时才会执行触发器                                                                                                   |
| STATUS | VARCHAR(8) | 触发器的使能状态<br>  * ENABLE：生效<br>  * DISABLED：失效                                                                             |
| DESCRIPTION | VARCHAR(4000) | 触发器的定义语句                                                                                                                 |
| ACTION_TYPE | CHAR(6)    | 固定为`PL/SQL`                                                                                                                |
| TRIGGER_BODY | CLOB       | 触发器的执行块                                                                                                                  |