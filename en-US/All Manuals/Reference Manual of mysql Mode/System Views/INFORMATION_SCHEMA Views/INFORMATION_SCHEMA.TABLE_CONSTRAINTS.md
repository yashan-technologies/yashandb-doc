This view shows the constraint information of the table.

|Field |Type |Description |
| --- | --- | --- |
| CONSTRAINT_CATALOG  | VARCHAR(3) | The name of the catalog to which the constraint belongs, this value is always def |
| CONSTRAINT_SCHEMA   | VARCHAR(64)| The name of the schema in which the constraint is located |
| CONSTRAINT_NAME     | VARCHAR(64)| The name of the constraint                          |
| TABLE_SCHEMA        | VARCHAR(64)| The name of the schema of the table where the constraint is located |
| TABLE_NAME          | VARCHAR(64)| The name of the table where the constraint is located |
| CONSTRAINT_TYPE     | VARCHAR(11)| The type of constraint (PRIMARY KEY, UNIQUE, FOREIGN KEY, CHECK) |