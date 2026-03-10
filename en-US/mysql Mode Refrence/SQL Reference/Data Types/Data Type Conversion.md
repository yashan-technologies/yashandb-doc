Data type conversion often occurs when the input type of an operation does not match the required type, or when an expression includes multiple different data types. In such cases, data types can be integrated through implicit conversion or explicit conversion.

## Implicit Conversion

The column headings in the table below represent the original types, while the row headings represent the target types. A ✓ indicates support for conversion, X indicates no support for conversion, and -- indicates no conversion is necessary.

(1)

|Data Type |**TINYINT**<br/>**BOOL** |**SMALLINT** |**INTEGER** |**BIGINT** |**TINYINT UNSIGNED** |**SMALLINT UNSIGNED** |**INTEGER UNSIGNED** |**BIGINT UNSIGNED** |
|-----------------------|----------------------|-----------------------|-----------------------|-------------|-----------------------|------------------------|----------------------|---------------------|
| **TINYINT**<br />**BOOL** | --                   | ✓                     | ✓                    | ✓           | ✓                     | ✓                     | ✓                    | ✓                   |
| **SMALLINT**          | ✓                    | --                    | ✓                    | ✓           | ✓                     | ✓                     | ✓                    | ✓                   |
| **INTEGER**           | ✓                    | ✓                     | --                   | ✓           | ✓                     | ✓                     | ✓                    | ✓                   |
| **BIGINT**            | ✓                    | ✓                     | ✓                    | --          | ✓                     | ✓                     | ✓                    | ✓                   |
| **TINYINT UNSIGNED**  | ✓                    | ✓                     | ✓                    | ✓           | --                    | ✓                     | ✓                    | ✓                   |
| **SMALLINT UNSIGNED** | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | --                     | ✓                    | ✓                   |
| **INTEGER UNSIGNED**  | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | --                    | ✓                  |
| **BIGINT UNSIGNED**   | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | --                  |
| **FLOAT**             | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **DOUBLE**            | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **DECIMAL**           | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **CHAR(SIZE)**        | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **VARCHAR(SIZE)**     | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **DATE**              | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **TIME**              | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **TIMESTAMP**         | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **BINARY(SIZE)**<br/>**VARBINARY(SIZE)**      | ✓                    | ✓                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **TINYTEXT**<br/>**MEDIUMTEXT**<br/>**TEXT**<br/>**LONGTEXT**              | ✓                    | ✓                     | ✓                    | ✓           | X                   | X                      | X                    | X                   |
| **TINYBLOB**<br/>**MEDIUMBLOB**<br/>**BLOB**<br/>**LONGBLOB**           | ✓                    | ✓                     | ✓                    | ✓           | X                    | X                      | X                    | X                   |

(2)

|Data Type |**FLOAT** |**DOUBLE** |**DECIMAL** |**CHAR(SIZE)** |**VARCHAR(SIZE)** |**DATE** |**TIME** |**TIMESTAMP** |
|-----------------------|----------------------|-----------------------|-----------------------|------------------|----------------------|-------------------------|----------------------|---------------------|
| **TINYINT**<br/>**BOOL** | ✓                   | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **SMALLINT**          | ✓                    | ✓                    | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **INTEGER**           | ✓                    | ✓                     | ✓                    | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **BIGINT**            | ✓                    | ✓                     | ✓                     | ✓              | ✓                    | ✓                      | ✓                    | ✓                   |
| **TINYINT UNSIGNED**  | ✓                    | ✓                     | ✓                     | ✓               | ✓                   | ✓                      | ✓                    | ✓                   |
| **SMALLINT UNSIGNED** | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                     | ✓                    | ✓                   |
| **INTEGER UNSIGNED**  | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                   | ✓                   |
| **BIGINT UNSIGNED**   | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                  |
| **FLOAT**             | --                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **DOUBLE**            | ✓                    | --                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **DECIMAL**           | ✓                    | ✓                     | --                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **CHAR(SIZE)**        | ✓                    | ✓                     | ✓                     | --               | ✓                    | ✓                      | ✓                    | ✓                   |
| **VARCHAR(SIZE)**     | ✓                    | ✓                     | ✓                     | ✓               | --                    | ✓                      | ✓                    | ✓                   |
| **DATE**              | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | --                      | ✓                    | ✓                   |
| **TIME**              | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | --                    | ✓                   |
| **TIMESTAMP**         | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | --                   |
| **BINARY(SIZE)**<br/>**VARBINARY(SIZE)** | ✓                    | ✓                     | ✓                     | ✓               | ✓                    | ✓                      | ✓                    | ✓                   |
| **TINYTEXT**<br/>**MEDIUMTEXT**<br/>**TEXT**<br/>**LONGTEXT** | X                    | X                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |
| **TINYBLOB**<br/>**MEDIUMBLOB**<br/>**BLOB**<br/>**LONGBLOB** | X                    | X                     | ✓                    | ✓           | ✓                    | ✓                      | ✓                    | ✓                   |

(3)

|Data Type |**BINARY(SIZE)** |**VARBINARY(SIZE)** |**TINYTEXT**<br/>**MEDIUMTEXT**<br/>**TEXT**<br/>**LONGTEXT** |**TINYBLOB**<br/>**MEDIUMBLOB**<br/>**BLOB**<br/>**LONGBLOB** |
|-----------------------|-----------------------|----------------------|----------------------|------------------------|
| **TINYINT**<br/>**BOOL** | ✓                     | ✓                   | ✓                    | X                      |
| **SMALLINT**          | ✓                     | ✓                   | ✓                    | X                      |
| **INTEGER**           | ✓                     | ✓                   | ✓                    | X                      |
| **BIGINT**            | ✓                     | ✓                   | ✓                    | X                      |
| **TINYINT UNSIGNED**  | ✓                     | ✓                   | X                    | X                      |
| **SMALLINT UNSIGNED** | ✓                     | ✓                   | X                    | X                      |
| **INTEGER UNSIGNED**  | ✓                     | ✓                   | X                   | X                      |
| **BIGINT UNSIGNED**   | ✓                     | ✓                   | X                    | X                      |
| **FLOAT**             | ✓                     | ✓                   | ✓                    | X                      |
| **DOUBLE**            | ✓                     | ✓                   | ✓                    | X                      |
| **DECIMAL**           | ✓                     | ✓                   | ✓                    | X                      |
| **CHAR(SIZE)**        | ✓                     | ✓                   | ✓                    | ✓                      |
| **VARCHAR(SIZE)**     | ✓                     | ✓                   | ✓                    | ✓                      |
| **DATE**              | ✓                     | ✓                   | ✓                    | ✓                      |
| **TIME**              | ✓                     | ✓                   | X                    | X                      |
| **TIMESTAMP**         | ✓                     | ✓                   | X                    | X                      |
| **BINARY(SIZE)**<br/>**VARBINARY(SIZE)** | --                    | ✓                    | ✓                    | ✓                      |
| **TINYTEXT**<br/>**MEDIUMTEXT**<br/>**TEXT**<br/>**LONGTEXT** | ✓                     | ✓                    | --                    | ✓                      |
| **TINYBLOB**<br/>**MEDIUMBLOB**<br/>**BLOB**<br/>**LONGBLOB** | ✓                     | ✓                    | ✓                    | --                     |

## Explicit Conversion

Explicit conversion is done by using type conversion functions to clearly specify the direction of conversion. Compared to implicit conversion, explicit conversion makes SQL statements easier to understand and enhances predictability of the output type.

The table below illustrates the type conversion functions supported by YashanDB in mysql mode:

|Function |Functionality |
|------------------------------------------------|---------------------------------|
| [BIN](../Built-in Functions/BIN)                             | Converts other types to BIT type (the result is returned in VARCHAR type containing the BIT value).                   |
| [CAST](../Built-in Functions/CAST)                           | Converts to the specified type.                      |
| [CONVERT](../Built-in Functions/CONVERT)                           | Converts to the specified type or character set.                      |

>**Note**:
>
>1. Columns of CHAR or VARCHAR type containing empty strings cannot be compared to or converted into numeric type columns.
>
>2. When a string-formatted float number is converted to an integer type through implicit or explicit conversion, the conversion will be performed by rounding according to the rounding rules.
