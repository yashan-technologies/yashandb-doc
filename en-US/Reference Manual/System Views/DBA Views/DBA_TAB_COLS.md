This view displays the column information for all tables (including the virtual column information created internally by the system).

|Field |Type |Description |
|----------------------------|---------------|---------------------------------------------------------|
| OWNER                      | VARCHAR(64)   | The username of the object to which this column belongs       |
| TABLE\_NAME                | VARCHAR(64)   | The name of the object to which this column belongs          |
| COLUMN\_NAME               | VARCHAR(64)   | Column name                                             |
| DATA\_TYPE                 | VARCHAR(64)   | Data type of the column                                   |
| DATA\_TYPE\_OWNER          | VARCHAR(64)   | The username of the object to which this data type belongs     |
| DATA\_LENGTH               | INTEGER       | The length of the column (unit: bytes)                    |
| DATA\_PRECISION            | INTEGER       | Column precision<br>\*   NUMBER type: decimal significant digits<br>\*   YMInterval type: significant digits for years<br>\*   DSInterval type: significant digits for days<br>\*   Other data types are NULL |
| DATA\_SCALE                | INTEGER       | Column scale<br>\*   NUMBER type: position of the lowest significant digit<br>\*   Time type: number of decimal seconds<br>\*   Other data types are NULL |
| NULLABLE                   | VARCHAR(1)    | Whether column values can be NULL<br>\*   Y: allows NULL<br>\*   N: does not allow NULL        |
| COLUMN_ID                  | INTEGER       | Sequence number of the column's creation                   |
| COLUMN\_COMPRESSION        | VARCHAR(12)   | Column compression type, empty indicates that the column does not support compressed storage, otherwise corresponds to the compression type                      |
| COLUMN\_COMPRESSION\_LEVEL | VARCHAR(6)    | Column compression level, empty indicates that compression level cannot be defined for this column, available levels are LOW, MEDIUM, and HIGH                  |
| COLUMN\_ENCODING           | VARCHAR(17)   | Column encoding method, empty indicates that encoding cannot be defined for this column, otherwise corresponds to the encoding type                                   |
| DATA\_DEFAULT              | VARCHAR(8000) | Default value for the column                                 |
| USER_GENERATED             | VARCHAR(1)    | Whether the column is user-created<br/>\*   Y: user created<br/>\*   N: system created           |
| DEFAULT_ON_NULL            | VARCHAR(1)    | Whether NULL values are allowed to be inserted<br/>\* Y: allowed<br/>\* N: not allowed        |
| IDENTITY_COLUMN            | VARCHAR(1)    | Whether the column has identity properties<br/>\* Y: is an identity column<br/>\* N: is not an identity column        |
| CHAR_USED                  | VARCHAR(1)    | Whether the column uses BYTE length semantic or CHAR length semantic<br/>\*   C: character length semantic<br/>\*   B: byte length semantic<br/>\*   NULL: this column is not of CHAR/VARCHAR/NCHAR/NVARCHAR data type |
| HIDDEN_COLUMN | VARCHAR(3) | Whether the column is visible<br/>\* YES: visible columns<br/>\* NO: invisible columns |
| VIRTUAL_COLUMN             | VARCHAR(3)    | Whether it is a virtual column<br/>\*   YES: virtual column<br/>\*   NO: not a virtual column  |
| SEGMENT_COLUMN_ID          | INTEGER       | Sequence number of the column in the row                    |
| CHAR_LENGTH                | INTEGER       | Character definition length of the column<br/>\*   This value is assigned based on the definition if the column is of CHAR/VARCHAR/NCHAR/NVARCHAR data type; otherwise, the value is 0 |
| CHAR_COL_DECL_LENGTH | INTEGER       | Declared length of character type columns                                                                          |
| DIMENSION                  | INTEGER       | Number of dimensions of the vector column<br/>This field is meaningful and has a valid value only when the column is of the VECTOR data type; it is always NULL for other data types                                 |
| VECTOR_INFO                | VARCHAR(33)   | Type definition information of the vector column, NULL for non-vector columns |