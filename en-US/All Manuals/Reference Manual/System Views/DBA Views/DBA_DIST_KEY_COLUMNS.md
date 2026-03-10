Starting from v23.2.1, this view has been deprecated. It was originally used to display information about all distributed keys.

|Field |Type |Description |
| --- |---------------| --- |
| OWNER          | VARCHAR(64)     | Owner of the distributed object      |
| NAME           | VARCHAR(64)     | Name of the distributed object       |
| COLUMN_NAME    | VARCHAR(64)     | Column name                         |
| COLUMN_POSITION | INTEGER         | Position of the column in the distributed key |