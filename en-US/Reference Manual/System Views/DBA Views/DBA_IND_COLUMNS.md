This view displays all the index column information.

|Field |Type |Description |
| --- | --- | --- |
| INDEX_OWNER    | VARCHAR(64) | The username of the index        |
| INDEX_NAME     | VARCHAR(64) | The index name                   |
| TABLE_OWNER    | VARCHAR(64) | The username of the table        |
| TABLE_NAME     | VARCHAR(64) | The table name                   |
| COLUMN_NAME    | VARCHAR(64) | The column name                  |
| COLUMN_POSITION | INTEGER     | The position of the column in the index |
| COLUMN_LENGTH  | INTEGER     | The length of the column (in bytes) |
| DESCEND        | VARCHAR(4)  | Whether the column is stored in descending order<br>*   DESC: descending<br>*   ASC: ascending |