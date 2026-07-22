This view displays information about identity columns of all tables.

|Field |Type |Description |
|----------------------------|---------------|---------------------------------------------------------|
| OWNER                      | VARCHAR(64)   | The user name of the object to which this column belongs  |
| TABLE\_NAME                | VARCHAR(64)   | The name of the object to which this column belongs       |
| COLUMN\_NAME               | VARCHAR(64)   | Column name                                             |
| GENERATION\_TYPE           | VARCHAR(10)   | The type of the identity column (BY DEFAULT or ALWAYS)   |
| SEQUENCE\_NAME             | VARCHAR(64)   | SEQUENCE name                                           |
| IDENTITY\_OPTIONS          | VARCHAR(318)  | Information related to the SEQUENCE for the identity column |