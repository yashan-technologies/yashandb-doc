This view displays all constraint information.

|Field |Type |Description |
| --- |---------------| --- |
| OWNER             | VARCHAR(64)     | User name of the constraint owner                                                                  |
| CONSTRAINT_NAME  | VARCHAR(64)     | Constraint name                                                                                    |
| CONSTRAINT_TYPE  | VARCHAR(6)      | Constraint type<br/>* P Primary key constraint<br/>* U Unique constraint<br/>* C Check constraint<br/>* R Foreign key constraint<br/>* O View with read only constraint<br/>* V View with check option constraint |
| TABLE_NAME       | VARCHAR(64)     | Table name                                                                                         |
| SEARCH_CONDITION  | VARCHAR(4000)   | Text of the check constraint condition                                                              |
| INDEX_NAME       | VARCHAR(64)     | Index name of the primary key or unique constraint                                                  |
| INDEX_OWNER      | VARCHAR(64)     | User name of the index owner for primary key or unique constraint                                   |
| R_OWNER          | VARCHAR(64)     | User name of the foreign key reference constraint owner                                             |
| R_CONSTRAINT_NAME | VARCHAR(64)   | Foreign key reference constraint name                                                                |
| DELETE_RULE      | VARCHAR(9)      | Foreign key cascade delete rule<br/>* NO ACTION: No action taken<br/>* CASCADE: Data referencing this value is also deleted<br/>* SET NULL: Corresponding column of data referencing this value is set to null |
| UPDATE_RULE      | VARCHAR(9)      | Foreign key cascade update rule<br/>* NO ACTION: No action taken<br/>* CASCADE: Data referencing this value is also updated<br/>* SET NULL: Corresponding column of data referencing this value is set to null |
| STATUS            | VARCHAR(8)      | Constraint status<br/>* ENABLED<br/>* DISABLED                                                    |
| DEFERRABLE        | CHAR(14)        | Whether deferrable check status<br/>* DEFERRABLE<br/>* NOT DEFERRABLE                            |
| DEFERRED          | CHAR(9)         | Check status<br/>* DEFERRED<br/>* IMMEDIATE                                                       |
| VALIDATED         | VARCHAR(13)     | Constraint check status<br/>* VALIDATED<br/>* NOT VALIDATED                                      |
| GENERATED         | VARCHAR(14)     | Constraint name rule<br/>* GENERATED NAME: System generated name<br/>* USER NAME: User specified name |
| DATABASE_MAINTAINED | VARCHAR(1)   | Whether DATABASE MAINTAINED<br/>* Y<br/>* N                                                       |