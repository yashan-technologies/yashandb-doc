This view shows the column information for all tables.

|Field |Type |Description |
|-------------------|---------------|------------------------------------------------------------------------------------------------------------|
| OWNER             | VARCHAR(64)    | The user name of the object to which this column belongs                                                          |
| TABLE_NAME       | VARCHAR(64)    | The name of the object to which this column belongs                                                              |
| COLUMN_NAME      | VARCHAR(64)    | Column name                                                                                                     |
| DATA_TYPE_OWNER | VARCHAR(64)    | The user name of the object to which this data type belongs                                                      |
| DATA_TYPE        | VARCHAR(64)    | The data type of the column                                                                                     |
| DATA_LENGTH      | INTEGER        | The length of the column (unit: bytes)                                                                          |
| DATA_PRECISION   | INTEGER        | The precision of the column<br>\*   NUMBER type: Decimal significant digits<br>\*   YMInterval type: Significant years<br>\*   DSInterval type: Significant days<br>\*   Other data types are NULL |
| DATA_SCALE       | INTEGER        | The scale of the column<br>\*   NUMBER type: The position of the least significant digit<br>\*   Time type: The number of decimal seconds<br>\*   Other data types are NULL                    |
| NULLABLE          | VARCHAR(1)     | Whether column values allow NULL<br>\*   Y: NULL is allowed<br>\*   N: NULL is not allowed                      |
| COLUMN_ID         | INTEGER        | The sequence number of column creation                                                                           |
| DATA_DEFAULT     | VARCHAR(8000)  | The default value of the column                                                                                 |
| DEFAULT_ON_NULL | VARCHAR(1)     | Whether the column value defaults to allow NULL<br>\* Y: NULL is allowed<br>\* N: NULL is not allowed            |
| IDENTITY_COLUMN  | VARCHAR(1)     | Whether the column value is an identity column<br>\* Y: It is an identity column<br>\* N: It is not an identity column |
| CHAR_USED         | VARCHAR(1)     | Whether the column uses BYTE length semantics or CHAR length semantics<br/>\*   C: Character length semantics<br/>\*   B: Byte length semantics<br/>\*   NULL: This column is not CHAR/VARCHAR/NCHAR/NVARCHAR data type |
| CHAR_LENGTH       | INTEGER        | The defined length of the column<br/>\*   When this column is of CHAR/VARCHAR/NCHAR/NVARCHAR data types, it is assigned based on the definition; for other types, the value of this column is 0 |
| CHAR_COL_DECL_LENGTH       | INTEGER       | The declared length of character-type columns<br/>\*   CHAR, VARCHAR2, CLOB type columns: byte length<br/>\*   NCHAR, NVARCHAR2, NCLOB type columns: character length<br/>\*   Non-character type columns: NULL                                    |