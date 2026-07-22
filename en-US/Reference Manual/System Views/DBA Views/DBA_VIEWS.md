This view displays the information of all views.

|Field |Type |Description |
|----------------------|-------------|--------------------------------------------------------------|
| OWNER                | VARCHAR(64)  | The username to which the view belongs                       |
| OWNERID              | INTEGER      | The user ID of the view owner                                 |
| VIEW\_NAME           | VARCHAR(64)  | The name of the view                                         |
| OBJECT\_ID           | BIGINT       | The object ID of the view                                    |
| DATABASE_MAINTAINED  | VARCHAR(1)    | Whether created by the database system <br>* Y: Yes <br>* N: No (created by the user) |
| TEXT\_LENGTH         | INTEGER      | The length of the view definition statement                   |
| TEXT                 | CLOB         | The SQL statement defining the view                           |
| READ\_ONLY           | VARCHAR(1)   | Whether the view is read-only                                 |