This view shows the privilege information for stored procedures or functions.

|Field |Type |Description |
| --- |----------------| --- |
| HOST          | VARCHAR(60)      | The name of the server where the database instance is located |
| DB            | VARCHAR(64)      | The name of the database              |
| USER          | VARCHAR(64)      | The username of the database user     |
| ROUTINE_NAME  | VARCHAR(64)      | The name of the stored procedure or function |
| ROUTINE_TYPE  | VARCHAR(9)       | The type of the stored procedure or function |
| GRANTOR       | VARCHAR(93)      | The username of the grantor           |
| PROC_PRIV     | LONGTEXT         | The privilege of the stored procedure or function |
| TIMESTAMP     | TIMESTAMP        | SCN                                   |