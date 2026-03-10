This view displays information about all function index expressions.

|Field |Type |Description |
|-------------------|---------------|----------------------------------|
| INDEX_OWNER       | VARCHAR(64)    | The username of the index            |
| INDEX_NAME        | VARCHAR(64)    | The name of the index                |
| TABLE_OWNER       | VARCHAR(64)    | The username of the table            |
| TABLE_NAME        | VARCHAR(64)    | The name of the table                |
| COLUMN_EXPRESSION | VARCHAR(8000)  | Function index expression             |
| COLUMN_POSITION   | INTEGER        | The position of the column in the index |