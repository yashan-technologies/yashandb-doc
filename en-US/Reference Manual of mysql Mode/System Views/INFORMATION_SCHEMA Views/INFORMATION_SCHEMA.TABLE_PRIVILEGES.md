This view shows the privilege information of the table.

|Field |Type |Description |
|----------------|--------------|--------------------|
| GRANTEE        | VARCHAR(81)   | The account name that is granted the privilege   |
| TABLE_CATALOG  | VARCHAR(512)  | The name of the catalog to which the schema belongs, this value is always def  |
| TABLE_SCHEMA   | VARCHAR(64)   | The name of the schema where the table is located     | 
| TABLE_NAME     | VARCHAR(64)   | The name of the table                |
| PRIVILEGE_TYPE | VARCHAR(64)   | The granted privilege                | 
| IS_GRANTABLE   | VARCHAR(3)    | Whether the privilege can be granted    |