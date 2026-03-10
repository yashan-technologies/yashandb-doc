This view displays SCHEMA privilege information.

|Field |Type |Description |
|----------------|--------------|---------------------|
| GRANTEE        | VARCHAR(81)   | The name of the account to which the privilege is granted  |
| TABLE_CATALOG  | VARCHAR(3)    | The name of the catalog to which the schema belongs; this value is always 'def' |
| TABLE_SCHEMA   | VARCHAR(64)   | The name of the schema where the SCHEMA is located        |
| PRIVILEGE_TYPE | VARCHAR(64)   | The privilege granted                | 
| IS_GRANTABLE   | VARCHAR(3)    | Whether the privilege can be granted             |