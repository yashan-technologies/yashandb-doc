This view displays all database link information.

|Field |Type |Description |
| --- | --- | --- |
| OWNER              | VARCHAR(64)   | The name of the user who owns the DBLink |
| DB_LINK            | VARCHAR(128)  | The name of the DBLink                   |
| USERNAME           | VARCHAR(128)  | The username used to access the DBLink   |
| PASSWORD           | RAW(256)      | The encrypted password used to access the DBLink |
| CREDENTIAL_NAME    | VARCHAR(128)  | The name of the DBLink credential<sup>*</sup> |
| CREDENTIAL_OWNER   | VARCHAR(128)  | The name of the user who owns the DBLink credential<sup>*</sup> |
| HOST               | VARCHAR(256)  | Primary database information of the target database that the DBLink connects to |
| CREATED            | DATE          | The creation time of the DBLink         |
| HIDDEN             | VARCHAR(3)    | Indicates whether the DBLink is hidden<sup>*</sup> |
| SHARD_INTERNAL     | VARCHAR(3)    | Indicates whether the DBLink is used to support ISC distributed databases<sup>*</sup> |
| VALID              | VARCHAR(3)    | Indicates whether the DBLink is valid. YES means valid, NO means invalid |
| INTRA_CDB          | VARCHAR(3)    | Indicates whether the DBLink spans containers<sup>*</sup> |

Columns marked with an asterisk (*) are reserved columns, and their internal values are always empty.