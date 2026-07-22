This view displays information about all authorized object privileges.

|Field |Type |Description |
| --- | --- | --- |
| GRANTEE     | VARCHAR(64)  | Name of the grantee              |
| OWNER       | VARCHAR(64)  | Name of the object owner         |
| TABLE_NAME  | VARCHAR(64)  | Name of the table                |
| TYPE        | VARCHAR(9)   | Object type<br>\*   TABLE <br>\*   VIEW<br>\*   SEQUENCE <br>\*   TYPE: User-Defined Type <br>\* PROCEDURE <br>\* UDF: User-Defined Function <br>\* PACKAGE |
| GRANTOR     | VARCHAR(64)  | Name of the grantor              |
| PRIVILEGE   | VARCHAR(64)  | Name of the granted object privilege |
| GRANTABLE   | VARCHAR(1)   | Whether it can be granted to other users<br>\*   Y<br>\*   N |
