This view displays information about available collations.

|Field |Type |Description |
|---------------------|-------------|------------------|
| COLLATION_NAME      | VARCHAR(32)   | Collation name             |
| CHARACTER_SET_NAME  | VARCHAR(32)   | Name of the character set associated with the collation |
| ID                  | BIGINT(38)    | Collation ID               | 
| IS_DEFAULT          | VARCHAR(3)    | Whether the collation is the default for its character set |
| IS_COMPILED         | VARCHAR(3)    | Whether it is compiled into the server |
| SORTLEN             | BIGINT(38)    | Memory required for sorting | 
