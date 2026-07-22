This view displays all the ST_GEOMETRY type column information with restrictions on subtype that the current user can access.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| OWNER        | VARCHAR(64)  | The username of the owner of the table containing this column                            |
| TABLE_NAME   | VARCHAR(64)  | The name of the table containing this column                                             |
| COLUMN_NAME  | VARCHAR(64)  | The name of the column                                                                    |
| SUBTYPE      | VARCHAR(18)  | The subtype of the column data, such as POINT                                           |
| HAS_Z        | TINYINT      | Indicates whether the column data contains a Z axis                                      |
| HAS_M        | TINYINT      | Indicates whether the column data contains an M axis                                      |
| SRID         | INTEGER      | The spatial reference system identifier of the column data                               |