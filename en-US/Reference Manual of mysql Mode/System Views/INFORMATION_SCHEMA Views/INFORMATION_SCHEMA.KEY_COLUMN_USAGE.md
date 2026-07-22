This view displays information about columns with constraints.

|Field |Type |Description |
| --- | --- | --- |
| CONSTRAINT\_CATALOG      | VARCHAR(3)  | Name of the catalog to which the constraint belongs; this value is always def                   |
| CONSTRAINT\_SCHEMA       | VARCHAR(64) | Name of the schema where the constraint is located                                             |
| CONSTRAINT\_NAME         | VARCHAR(64) | Name of the constraint                                                                            |
| TABLE\_CATALOG           | VARCHAR(3)  | Name of the catalog to which the table of the constraint belongs; this value is always def     |
| TABLE\_SCHEMA            | VARCHAR(64) | Name of the schema of the table to which the constraint belongs                                 |
| TABLE\_NAME              | VARCHAR(64) | Name of the table to which the constraint belongs                                               |
| COLUMN\_NAME             | VARCHAR(64) | Name of the column with the constraint<br/>If the constraint is a foreign key, this item is the column of the foreign key (not the referenced column) |
| ORDINAL\_POSITION        | BIGINT      | Position of the column in the constraint, column positions start numbering from 1              |
| POSITION\_IN\_UNIQUE\_CONSTRAINT | BIGINT | For unique and primary key constraints, this item is NULL<br/>For foreign key constraints, this item is the position of the column in the foreign key relationship |
| REFERENCED\_TABLE\_SCHEMA| VARCHAR(64) | For unique and primary key constraints, this item is NULL<br/>For foreign key constraints, this item is the schema name of the table referenced by the foreign key |
| REFERENCED\_TABLE\_NAME  | VARCHAR(64) | For unique and primary key constraints, this item is NULL<br/>For foreign key constraints, this item is the name of the table referenced by the foreign key  |
| REFERENCED\_COLUMN\_NAME | VARCHAR(64) | For unique and primary key constraints, this item is NULL<br/>For foreign key constraints, this item is the name of the column referenced by the foreign key  |