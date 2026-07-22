This view displays information about foreign key constraints.

|Field |Type |Description |
| --- | --- | --- |
| CONSTRAINT\_CATALOG          | VARCHAR(3) | The catalog name to which the foreign key constraint belongs; this value is always def |
| CONSTRAINT\_SCHEMA           | VARCHAR(64)| The schema name where the foreign key constraint resides      |
| CONSTRAINT\_NAME             | VARCHAR(64)| The name of the foreign key constraint                        |
| UNIQUE\_CONSTRAINT\_CATALOG  | VARCHAR(3) | The catalog name to which the foreign key referenced constraint belongs; this value is always def |
| UNIQUE\_CONSTRAINT\_SCHEMA   | VARCHAR(64)| The schema name where the foreign key referenced constraint resides |
| UNIQUE\_CONSTRAINT\_NAME     | VARCHAR(64)| The name of the foreign key referenced constraint             |
| MATCH\_OPTION                | VARCHAR(4) | The value of the foreign key constraint property; the only valid value is NONE |
| UPDATE\_RULE                 | VARCHAR(9) | The cascade update rule for the foreign key constraint (RESTRICT, CASCADE, SET NULL) |
| DELETE\_RULE                 | VARCHAR(9) | The cascade delete rule for the foreign key constraint (RESTRICT, CASCADE, SET NULL) |
| TABLE\_NAME                  | VARCHAR(64)| The name of the table where the foreign key constraint resides |
| REFERENCED\_TABLE\_NAME      | VARCHAR(64)| The name of the table where the foreign key referenced constraint resides |