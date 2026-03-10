This view displays relevant information about the bound variables in the buffer.

|Field |Type |Description |
|-----------------|---------------|---------------------------------------------|
| GROUP_ID        | NUMBER         | Group ID                                      |
| GROUP_NODE_ID   | NUMBER         | Node ID within the group                      |
| INST_ID         | NUMBER         | Instance ID                                   |
| ADDRESS         | RAW(8)         | SQL address                                   |
| HASH_VALUE      | BIGINT         | Hash value of the SQL, calculated from the SQL text |
| SQL_ID          | VARCHAR(13)    | Unique identifier for a SQL statement, obtained through hash/encryption of SQL text |
| CHILD_ADDRESS    | RAW(8)        | Child cursor address                           |
| CHILD_NUMBER    | INTEGER        | Child cursor number                            |
| NAME            | VARCHAR(64)    | Name of the bound variable (reserved field)  |
| POSITION        | INTEGER        | Position of the bound variable in the SQL     |
| DUP_POSITION    | INTEGER        | If this bound variable is used multiple times in SQL, this column's value is set to the position of the first encountered bound variable (reserved field) |
| DATATYPE        | INTEGER        | Internal identifier for the data type of the bound variable |
| DATATYPE_STRING | VARCHAR(32)    | Text representation of the data type of the bound variable |
| CHARACTER_SID   | INTEGER        | National/locale character set identifier (reserved field) |
| PRECISION       | INTEGER        | Precision of the bound variable (reserved field) |
| SCALE           | INTEGER        | Scale of the bound variable (reserved field) |
| MAX_LENGTH      | INTEGER        | Maximum length of the bound variable          |
| WAS_CAPTURED    | VARCHAR(3)     | Indicates whether the value of the bound variable was captured (reserved field) |
| LAST_CAPTURED   | DATE           | Time of the last capture of the bound variable (reserved field) |
| VALUE_STRING    | VARCHAR(4000)  | Value of the bound variable, represented as a string (reserved field) |