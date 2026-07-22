This view displays information about all external tables.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER | VARCHAR(64) | Username of the external table owner |
| TABLE_NAME | VARCHAR(64) | Name of the external table |
| TYPE_OWNER | CHAR(3)     | Username of the external table driver owner |
| TYPE_NAME | VARCHAR(12) | Name of the external table driver |
| DEFAULT_DIRECTORY_OWNER | CHAR(3)     | Username of the default directory owner of the external table |
| DEFAULT_DIRECTORY_NAME | VARCHAR(64) | Name of the default directory for the external table |
| REJECT_LIMIT | VARCHAR(11) | Error tolerance limit for the external table (number of data errors that can be ignored)<br>* Number <br>* UNLIMITED |
| ACCESS_TYPE | VARCHAR(7)  | Access parameter type: <br>* BLOB <br>* CLOB |
| ACCESS_PARAMETERS | CLOB        | Access parameters for the external table |
| PROPERTY | VARCHAR(10) | External table property: <br>* REFERENCED <br>* ALL |