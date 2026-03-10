This view displays all trigger information.

|Field |Type |Description |
| --- |------------|--------------------------------------------------------------------------------------------------------------------------|
| OWNER            | VARCHAR(64)  | The username to which the trigger belongs                                                                                        |
| TRIGGER_NAME     | VARCHAR(64)  | The name of the trigger                                                                                                        |
| TRIGGER_TYPE     | VARCHAR(16)  | The timing of the trigger<br> * BEFORE STATEMENT: Before the statement execution<br> * BEFORE EACH ROW: Before each row execution<br> * AFTER STATEMENT: After the statement execution<br> * AFTER EACH ROW: After each row execution |
| TRIGGERING_EVENT | VARCHAR(26)  | The triggering event of the trigger                                                                                             |
| TABLE_OWNER      | VARCHAR(64)  | The username to which the object (i.e., a specific table) containing the trigger belongs                                       |
| BASE_OBJECT_TYPE | VARCHAR(9)   | The type of the object containing the trigger, currently fixed as `TABLE`                                                       |
| TABLE_NAME       | VARCHAR(64)  | The name of the base object when the trigger's base object is a table                                                          |
| COLUMN_NAME      | VARCHAR(1)   | Reserved field, value is NULL                                                                                                   |
| REFERENCING_NAMES | VARCHAR(283) | The correlation names of OLD and NEW within the trigger                                                                         |
| WHEN_CLAUSE      | VARCHAR(4000) | The triggering condition of the trigger; the trigger will only execute when this condition is true                               |
| STATUS           | VARCHAR(8)   | The enablement status of the trigger<br>  * ENABLE: Effective<br>  * DISABLED: Ineffective                                     |
| DESCRIPTION      | VARCHAR(4000) | The definition statement of the trigger                                                                                         |
| ACTION_TYPE      | CHAR(6)      | Fixed as `PL/SQL`                                                                                                              |
| TRIGGER_BODY     | CLOB         | The execution block of the trigger                                                                                              |