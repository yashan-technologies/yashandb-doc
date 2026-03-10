This view displays all OUTLINE information.

|Field |Type |Description |
| --- | --- | --- |
| NAME    | VARCHAR(64)   | Object name                                   |
| OWNER   | VARCHAR(64)   | Owner username of the object                  |
| CATEGORY| VARCHAR(64)   | Category name                                 |
| USED    | VARCHAR(6)    | Whether it is used                            |
| TIMESTAMP| TIMESTAMP(6)    | Object creation time                          |
| VERSION | VARCHAR(64)   | Current version information of the database   |
| SQL_TEXT| CLOB          | SQL statement corresponding to the SQL_TEXT syntax OUTLINE |
| COMPATIBLE| VARCHAR(12) | Whether the OUTLINE is compatible during migration |
| ENABLED | VARCHAR(8)    | Whether it is enabled                         |
| FORMAT  | VARCHAR(6)    | Hint format                                   |
| MIGRATED| VARCHAR(12)   | Whether the OUTLINE has been migrated        |
| SQL_ID  | VARCHAR(13)   | SQL_ID value corresponding to the SQL statement ID of the SQL_ID syntax OUTLINE |