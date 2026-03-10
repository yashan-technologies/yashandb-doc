This view displays information about triggers.

|Field |Type |Description |
|----------------------------|-------------|--------------------------------------------------------------|
| TRIGGER_CATALOG            | VARCHAR(3)    | The catalog name of the trigger, this value is always def     |
| TRIGGER_SCHEMA             | VARCHAR(64)   | The schema name of the trigger                                 |
| TRIGGER_NAME               | VARCHAR(64)   | The name of the trigger                                       |
| EVENT_MANIPULATION         | VARCHAR(26)   | The trigger event, values can be INSERT, DELETE, UPDATE       |
| EVENT_OBJECT_CATALOG       | VARCHAR(3)    | The catalog of the table associated with the trigger, this value is always def |
| EVENT_OBJECT_SCHEMA        | VARCHAR(64)   | The schema of the table associated with the trigger           |
| EVENT_OBJECT_TABLE         | VARCHAR(64)   | The name of the table associated with the trigger             |
| ACTION_ORDER               | VARCHAR(20)   | The position in the list of triggers with the same EVENT_MANIPULATION and ACTION_TIMING values on the same table<br>Syntax compatible only, no actual functional meaning |
| ACTION_CONDITION           | VARCHAR(4)    | This value is always NULL                                      |
| ACTION_STATEMENT           | LONGTEXT      | The body of the trigger; that is, the statement executed when the trigger is activated |
| ACTION_ORIENTATION         | VARCHAR(3)    | This value is always ROW                                      |
| ACTION_TIMING              | VARCHAR(6)    | The timing of the trigger activation<br/>* BEFORE: before the triggering event<br/>* AFTER: after the triggering event |
| ACTION_REFERENCE_OLD_TABLE | VARCHAR(4)    | This value is always NULL                                      |
| ACTION_REFERENCE_NEW_TABLE | VARCHAR(4)    | This value is always NULL                                      |
| ACTION_REFERENCE_OLD_ROW   | VARCHAR(3)    | The old row identifier, this value is always OLD              |
| ACTION_REFERENCE_NEW_ROW   | VARCHAR(3)    | The new row identifier, this value is always NEW              |
| CREATED                    | VARCHAR(20)   | The creation date and time of the trigger<br>Syntax compatible only, no actual functional meaning                     |
| SQL_MODE                   | VARCHAR(20)   | The SQL_MODE in effect when the trigger was created, the trigger will execute under this mode<br>Syntax compatible only, no actual functional meaning |
| DEFINER                    | VARCHAR(64)   | The account named in the DEFINER clause (usually the schema who created the trigger)<br>Syntax compatible only, no actual functional meaning |
| CHARACTER_SET_CLIENT       | VARCHAR(20)   | The session value of the system variable CHARACTER_SET_CLIENT when the trigger was created<br>Syntax compatible only, no actual functional meaning |
| COLLATION_CONNECTION       | VARCHAR(20)   | The session value of the system variable COLLATION_CONNECTION when the trigger was created<br>Syntax compatible only, no actual functional meaning |
| DATABASE_COLLATION         | VARCHAR(20)   | The collation of the database associated with the trigger<br>Syntax compatible only, no actual functional meaning     |