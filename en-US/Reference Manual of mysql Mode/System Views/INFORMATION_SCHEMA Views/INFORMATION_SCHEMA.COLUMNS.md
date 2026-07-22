This view displays column information.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_CATALOG            | VARCHAR(3)    | The catalog name of the table containing the column |
| TABLE_SCHEMA             | VARCHAR(64)   | The schema name of the table containing the column |
| TABLE_NAME               | VARCHAR(64)   | The name of the table containing the column |
| COLUMN_NAME              | VARCHAR(64)   | The name of the column                     |
| ORDINAL_POSITION         | BIGINT        | The position of the column in the table    |
| COLUMN_DEFAULT           | VARCHAR(32000) | The default value of the column            |
| IS_NULLABLE              | VARCHAR(3)    | Whether the column allows null values      |
| DATA_TYPE    | varchar(65534)          | The data type of the column                |
| CHARACTER_MAXIMUM_LENGTH | BIGINT        | The maximum length of character type columns |
| CHARACTER_OCTET_LENGTH   | BIGINT        | The number of bytes used to store characters in character type columns |
| NUMERIC_PRECISION        | BIGINT        | The maximum number of digits allowed for numeric columns |
| NUMERIC_SCALE            | BIGINT        | The number of digits to the right of the decimal point in numeric data types |
| DATETIME_PRECISION       | BIGINT        | The number of digits to the right of the decimal point in datetime data types |
| CHARACTER_SET_NAME       | VARCHAR(32)   | The character set name (current setting is invalid and cannot be changed) |
| COLLATION_NAME           | VARCHAR(32)   | The collation name (current setting is invalid and cannot be changed) |
| COLUMN_TYPE     | varchar(65534)         | Column data type            |
| COLUMN_KEY               | VARCHAR(12)   | Whether the column is part of the primary key or unique index of the table |
| EXTRA                    | VARCHAR(120)  | Additional attributes of the column        |
| PRIVILEGES               | VARCHAR(1)    | The privileges owned on the column<br>Syntax compatible only, no actual functional meaning.         |
| COLUMN_COMMENT           | VARCHAR(4096) | Comment or description of the column       |
| GENERATION_EXPRESSION    | VARCHAR(1)    | The computed expression of the generated column<br>Syntax compatible only, no actual functional meaning. |