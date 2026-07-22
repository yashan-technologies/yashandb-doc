This view displays all database link information.

|Field |Type |Description |
| --- | --- | --- |
| OWNER              | VARCHAR(64)   | The name of the user who owns the DBLINK |
| DB_LINK            | VARCHAR(128)  | The name of the DBLINK                   |
| USERNAME           | VARCHAR(128)  | The username used to access the DBLINK   |
| PASSWORD           | RAW(256)      | The encrypted password used to access the DBLINK |
| CREDENTIAL_NAME    | VARCHAR(128)  | The name of the DBLINK credential<sup>*</sup> |
| CREDENTIAL_OWNER   | VARCHAR(128)  | The name of the user who owns the DBLINK credential<sup>*</sup> |
| HOST               | VARCHAR(256)  | Primary database information of the target database that the DBLINK connects to |
| CREATED            | DATE          | The creation time of the DBLINK         |
| HIDDEN             | VARCHAR(3)    | Indicates whether the DBLINK is hidden<sup>*</sup> |
| SHARD_INTERNAL     | VARCHAR(3)    | Indicates whether the DBLINK is used to support ISC distributed databases<sup>*</sup> |
| VALID              | VARCHAR(3)    | Indicates whether the DBLINK is valid. YES means valid, NO means invalid |
| INTRA_CDB          | VARCHAR(3)    | Indicates whether the DBLINK spans containers<sup>*</sup> |

Columns marked with an asterisk (*) are reserved columns, and their internal values are always empty.