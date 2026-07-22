This view displays all synonym information in the database.

|Field |Type |Description |
| --- | --- | --- |
| OWNER          | VARCHAR(64) | The username to which the synonym belongs     |
| SYNONYM_NAME   | VARCHAR(64) | The name of the synonym                       |
| TABLE_OWNER    | VARCHAR(64) | The username of the referenced object         |
| TABLE_NAME     | VARCHAR(64) | The name of the referenced object             |
| DB_LINK        | VARCHAR(64) | The name of the database link being referenced |
| DATABASE_MAINTAINED  | VARCHAR(1)    | Whether created by the database system <br>* Y: Yes <br>* N: No (created by the user) |