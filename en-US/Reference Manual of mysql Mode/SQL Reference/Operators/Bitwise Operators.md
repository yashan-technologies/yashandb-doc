YashanDB provides the following bitwise operators:

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| &   | Binary  | Bitwise AND: 1 & 1 = 1<br><br>Others are 0 | Result is NULL |
| &#124; | Binary  | Bitwise OR: 0 &#124; 0 = 0<br><br>Others are 1 | Result is NULL |
| ^   | Binary  | Bitwise XOR: 0 ^ 0 = 0 or 1 ^ 1 = 0<br><br>Others are 1 | Result is NULL |

In YashanDB, bitwise operations can be achieved through:

*   Bitwise operators: & , | , ^
*   Built-in functions: [BITAND BITOR BITXOR](../../../Development Guide/SQL Reference Manual/Built-in Functions/BITAND BITOR BITXOR)

Here, & is synonymous with BITAND, | is synonymous with BITOR, and ^ is synonymous with BITXOR.

For the two data involved in the operation, they are expanded in binary and the bitwise operation is performed bit by bit from low to high, outputting the operation result as BIGINT type.

Bitwise operations support up to 64 bits.

Decimal participation: [FLOOR](../Built-in Functions/FLOOR) truncates the decimal before participating in bitwise operations.

Operator Precedence
-----

From highest to lowest, the operator precedence is: ^ > & > |.

Double parentheses () can be used to adjust the desired operator precedence.

Data Types
----

The following data types may participate in bitwise operations:

*   Numeric types (except FLOAT, DOUBLE)
*   String types

### Rules of Operations

When performing binary bitwise operations, YashanDB will unify all participating data types to BIGINT or BIGINT UNSIGNED type through implicit data conversion before performing the bitwise operation.

The table below illustrates the specific unification rules: (Row and column headers are the data types participating in bitwise operations; content cells are the unified data types; – indicates that the two data types are not supported for bitwise operation)

|Data Type |TINYINT         |TINYINT UNSIGNED |SMALLINT        |SMALLINT UNSIGNED |INT (MEDIUMINT)   |INT (MEDIUMINT) UNSIGNED     |BIGINT       |BIGINT UNSIGNED |
| ---------------------- | ----------------- | ------------------ | ----------------- | ------------------- | ----------------- | ----------------- | ----------------- | ----------------- |
| TINYINT                | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| TINYINT UNSIGNED     | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| SMALLINT               | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| SMALLINT UNSIGNED    | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| INT                    | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| INT UNSIGNED         | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| BIGINT                 | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| BIGINT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| DECIMAL                | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| FLOAT                  | -                 | BIGINT UNSIGNED  | -                 | BIGINT UNSIGNED   | -                 | BIGINT UNSIGNED | -                 | BIGINT UNSIGNED |
| DOUBLE                 | -                 | BIGINT UNSIGNED  | -                 | BIGINT UNSIGNED   | -                 | BIGINT UNSIGNED | -                 | BIGINT UNSIGNED |
| DATE                   | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| CHAR                   | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| NCHAR                  | BIGINT            | -                  | BIGINT            | -                   | BIGINT            | -                 | BIGINT            | -                 |
| TIMESTAMP              | -                 | BIGINT UNSIGNED  | -                 | BIGINT UNSIGNED   | -                 | BIGINT UNSIGNED | -                 | BIGINT UNSIGNED |
| TIME                   | BIGINT            | BIGINT UNSIGNED  | BIGINT            | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| CLOB                   | -                 | -                  | -                 | -                   | -                 | -                 | -                 | -                 |


|Data Type |DECIMAL   |FLOAT        |DOUBLE     |DATE     |CHAR    |NCHAR  |TIMESTAMP    |TIME      |CLOB      |
| ---------------------- | ----------------- | ----------------- | ----------------- | ----------------- | ----------------- | ------ | ----------------- | ----------------- | ----------------- |
| TINYINT                | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| TINYINT UNSIGNED     | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | BIGINT UNSIGNED | BIGINT UNSIGNED | -                 |
| SMALLINT               | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| SMALLINT UNSIGNED    | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | BIGINT UNSIGNED | BIGINT UNSIGNED | -                 |
| INT                    | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| INT UNSIGNED         | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | BIGINT UNSIGNED | BIGINT UNSIGNED | -                 |
| BIGINT                 | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| BIGINT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | BIGINT UNSIGNED | BIGINT UNSIGNED | -                 |
| DECIMAL                | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| FLOAT                  | -                 | -                 | -                 | BIGINT UNSIGNED | -                 | -      | -                 | BIGINT            | -                 |
| DOUBLE                 | -                 | -                 | -                 | BIGINT UNSIGNED | -                 | -      | -                 | BIGINT            | -                 |
| DATE                   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| CHAR                   | BIGINT            | -                 | -                 | BIGINT UNSIGNED | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| NCHAR                  | BIGINT            | -                 | -                 | -                 | BIGINT            | BIGINT | -                 | BIGINT            | -                 |
| TIMESTAMP              | -                 | -                 | -                 | BIGINT UNSIGNED | -                 | -      | -                 | BIGINT UNSIGNED | -                 |
| TIME                   | BIGINT            | BIGINT            | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT | BIGINT UNSIGNED | BIGINT            | -                 |
| CLOB                   | -                 | -                 | -                 | BIGINT UNSIGNED | -                 | -      | -                 | -                 | -                 |