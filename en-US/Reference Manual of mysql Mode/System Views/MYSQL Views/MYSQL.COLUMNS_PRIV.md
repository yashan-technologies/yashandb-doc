This view displays column-level privilege information.

YashanDB has not yet opened column-level permission configuration, so the query result of this view is empty.

|Field |Type |Description |
| --- | --- | --- |
| HOST          | VARCHAR(1020)| The name of the server where the database instance is located |
| DB            | VARCHAR(64) | The name of the database                   |
| USER          | VARCHAR(64) | The username of the database user         |
| TABLE_NAME    | VARCHAR(64) | The name of the table                     |
| COLUMN_NAME    | VARCHAR(64) | The name of the column                    |
| TIMESTAMP     | TIMESTAMP    | SCN                                      |
| COLUMN_PRIV   | VARCHAR(1)  | The privilege of the column               |