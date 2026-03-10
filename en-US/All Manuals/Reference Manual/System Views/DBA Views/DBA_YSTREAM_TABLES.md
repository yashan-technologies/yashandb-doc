This view displays the whitelist of tables and schemas configured for the YStream server.

|Field |Type |Description |
| --- | --- | --- |
| SERVER_ID  | INTEGER      | Service ID                                           |
| SCHEMA     | VARCHAR(64)  | Schema                                               |
| TABLE_NAME | VARCHAR(64)  | Table name. If the table name is empty, it indicates that all tables under this schema are included in the whitelist. |