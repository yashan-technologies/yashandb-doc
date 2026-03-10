This view displays privilege information at the table level.

|Field |Type |Description |
| --- | --- | --- |
| HOST         | VARCHAR(60)  | The name of the server where the database instance resides |
| DB           | VARCHAR(64)  | The name of the database                           |
| USER         | VARCHAR(64)  | The username of the database user                  |
| TABLE_NAME   | VARCHAR(64)  | The name of the table                             |
| GRANTOR      | VARCHAR(93)  | The username of the grantor                       |
| TIMESTAMP    | TIMESTAMP    | SCN                                             |
| TABLE_PRIV   | VARCHAR(1500)| The privileges of the table                       |
| COLUMN_PRIV  | CHAR(0)     | The privileges of the column                      |