This view displays relevant information about the bound variables in the library buffer.

|Field |Type |Description |
|--------------------|---------------|---------------------|
| ADDRESS            | RAW(8)          | SQL address                               |
| HASH_VALUE        | BIGINT          | Hash value of SQL, calculated from SQL text  |
| SQL_ID            | VARCHAR(13)     | Unique identifier for a SQL statement, obtained through hashing/encryption of SQL text |
| CHILD_ADDRESS     | RAW(8)          | Child cursor address                       |
| CHILD_NUMBER      | INTEGER         | Child cursor number                        |
| NAME               | VARCHAR(64)     | Name of the bound variable                 |
| POSITION           | INTEGER         | Position of the bound variable in SQL     |
| DUP_POSITION      | INTEGER         | If the bound variable is used multiple times in SQL, this column's value is set to the position of the first encountered bound variable |
| DATATYPE           | INTEGER         | Internal identifier for the bound variable's data type |
| DATATYPE_STRING    | VARCHAR(32)     | Text representation of the bound variable's data type |
| CHARACTER_SID      | INTEGER         | National/Regional character set identifier (reserved field) |
| PRECISION          | INTEGER         | Precision of the bound variable (valid for numeric types) |
| SCALE              | INTEGER         | Scale of the bound variable (valid for numeric types) |
| MAX_LENGTH        | INTEGER         | Maximum length of the bound variable      |
| WAS_CAPTURED      | VARCHAR(3)      | Indicates whether the value of the bound variable was captured |
| LAST_CAPTURED     | DATE            | Time of the last capture of the bound variable (interval affected by hidden parameter _CURSOR_BIND_CAPTURE_INTERVAL) |
| VALUE_STRING      | VARCHAR(4000)   | Value of the bound variable, represented as a string |