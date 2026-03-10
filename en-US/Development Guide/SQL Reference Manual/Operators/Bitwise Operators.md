YashanDB provides the following bitwise operators:

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| &   | Binary  | Bitwise AND: 1 & 1 = 1<br><br>Others are 0 | Result is NULL |
| &#124; | Binary  | Bitwise OR: 0 &#124; 0 = 0<br><br>Others are 1 | Result is NULL |
| ^   | Binary  | Bitwise XOR: 0 ^ 0 = 0 or 1 ^ 1 = 0<br><br>Others are 1 | Result is NULL |

In YashanDB, bitwise operations can be achieved through:

*   Bitwise operators: & , | , ^
*   Built-in functions: [BITAND BITOR BITXOR](../Built-in Functions (yashan Mode)/BITAND BITOR BITXOR)

Here, & is synonymous with BITAND, | is synonymous with BITOR, and ^ is synonymous with BITXOR.

For the two data involved in the operation, they are expanded in binary and the bitwise operation is performed bit by bit from low to high, outputting the operation result as BIGINT type.

Bitwise operations support up to 64 bits.

Decimal participation: [FLOOR](../Built-in Functions (yashan Mode)/FLOOR) truncates the decimal before participating in bitwise operations.

Operator Precedence
-----

From highest to lowest, the operator precedence is: ^ > & > |.

Double parentheses () can be used to adjust the desired operator precedence.

Data Types
----

The following data types may participate in bitwise operations:

*   Numeric types (except FLOAT, DOUBLE)
*   String types

When performing binary bitwise operations, YashanDB will unify all participating data types to BIGINT type through implicit data conversion before performing the bitwise operation.

The table below illustrates the specific unification rules: (Row and column headers are the data types participating in bitwise operations; content cells are the unified data types; – indicates that the two data types are not supported for bitwise operation)

|Data Type |TINYINT |SMALLINT |INT |BIGINT |NUMBER |String Type |BIT |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **TINYINT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **SMALLINT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **INT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **BIGINT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **NUMBER** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **String Type** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
| **BIT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT |
