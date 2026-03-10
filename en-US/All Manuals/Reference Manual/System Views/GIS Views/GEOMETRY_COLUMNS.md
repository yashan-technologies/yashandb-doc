This view displays information about all ST_GEOMETRY type columns accessible to the current user.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| F_TABLE_CATALOG    | VARCHAR(64)   | The name of the database to which the table containing this column belongs       |
| F_TABLE_SCHEMA     | VARCHAR(64)   | The owner username of the table containing this column                           |
| F_TABLE_NAME       | VARCHAR(64)   | The name of the table containing this column                                     |
| F_GEOMETRY_COLUMN  | VARCHAR(64)   | The column name                                                                   |
| COORD_DIMENSION    | NUMBER        | The dimension of the column data                                                 |
| SRID               | INTEGER       | The identifier for the spatial reference system of the column data               |
| TYPE               | VARCHAR(19)   | The subtype of the column data, such as POINT                                   |