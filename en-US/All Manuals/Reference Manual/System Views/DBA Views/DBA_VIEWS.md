This view displays the information of all views.

|Field |Type |Description |
|----------------------|-------------|--------------------------------------------------------------|
| OWNER                | VARCHAR(64)  | The username to which the view belongs                       |
| OWNERID              | INTEGER      | The user ID of the view owner                                 |
| VIEW_NAME           | VARCHAR(64)  | The name of the view                                         |
| OBJECT_ID           | BIGINT       | The object ID of the view                                    |
| DATABASE_MAINTAINED | VARCHAR(1)   | Whether it is DATABASE MAINTAINED<br>\*   Y: View created by the database system<br>\*   N: View created by the user |
| TEXT_LENGTH         | INTEGER      | The length of the view definition statement                   |
| TEXT                 | CLOB         | The SQL statement defining the view                           |
| READ_ONLY           | VARCHAR(1)   | Whether the view is read-only                                 |