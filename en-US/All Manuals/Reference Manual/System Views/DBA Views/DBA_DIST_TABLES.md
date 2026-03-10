Since v23.2.1, this view has been deprecated. It was originally used to display information about all sharded tables.

|Field |Type |Description |
| --- |---------------| --- |
| OWNER       | VARCHAR(64)   | The username of the table's owner |
| TABLE_NAME  | VARCHAR(64)   | The name of the table        |
| DIST_TYPE   | VARCHAR(10)   | Distribution type<br/>* RANGE<br/>* HASH<br/>* LIST<br/>* ROUNDRROBIN |
| DIST_KEY_COUNT | INTEGER    | The number of distribution keys |