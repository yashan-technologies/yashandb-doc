This view displays information about foreign key constraints.

|Field |Type |Description |
| --- | --- | --- |
| CONSTRAINT_CATALOG          | VARCHAR(3) | The catalog name to which the foreign key constraint belongs; this value is always def |
| CONSTRAINT_SCHEMA           | VARCHAR(64)| The schema name where the foreign key constraint resides      |
| CONSTRAINT_NAME             | VARCHAR(64)| The name of the foreign key constraint                        |
| UNIQUE_CONSTRAINT_CATALOG  | VARCHAR(3) | The catalog name to which the foreign key referenced constraint belongs; this value is always def |
| UNIQUE_CONSTRAINT_SCHEMA   | VARCHAR(64)| The schema name where the foreign key referenced constraint resides |
| UNIQUE_CONSTRAINT_NAME     | VARCHAR(64)| The name of the foreign key referenced constraint             |
| MATCH_OPTION                | VARCHAR(4) | The value of the foreign key constraint property; the only valid value is NONE |
| UPDATE_RULE                 | VARCHAR(8) | The cascade update rule for the foreign key constraint (RESTRICT, CASCADE, SET NULL) |
| DELETE_RULE                 | VARCHAR(8) | The cascade delete rule for the foreign key constraint (RESTRICT, CASCADE, SET NULL) |
| TABLE_NAME                  | VARCHAR(64)| The name of the table where the foreign key constraint resides |
| REFERENCED_TABLE_NAME      | VARCHAR(64)| The name of the table where the foreign key referenced constraint resides |