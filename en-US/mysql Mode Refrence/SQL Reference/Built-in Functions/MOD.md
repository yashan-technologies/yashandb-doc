```ebnf+diagram
mod::= MOD "(" expr1 "," expr2 ")"
```
MOD is the modulus function, consistent with the [arithmetic operator](../Operators/Arithmetic Operators) algorithm. 

The mapping relationship of input parameter types to return types is as follows:

|expr1 |expr2 |Return Value |
|-------------------------------|-------------------|---------|
| CHAR, DOUBLE, FLOAT, BINARY   | Any                | DOUBLE       |
| Any                            | CHAR, DOUBLE, FLOAT, BINARY | DOUBLE   |
| NUMBER, DATE                  | Any                | NUMBER       |
| Any                            | NUMBER, DATE      | NUMBER       |
| BOOL, TINYINT, SMALLINT, INTEGER | Any            | INTEGER      |
| BIGINT, BIT                   | Any                | BIGINT       |

**expr1, expr2**

[General expression](../General SQL Syntax/expr), the data types of the values are as shown in the input parameter types table above.

When multiple input rules from different rows are satisfied, the matching rule will prioritize from top to bottom. For example, if expr1 is of BIT type and expr2 is of CHAR type, it will return DOUBLE type.

- When the value of expr1 or expr2 is of DATE type, the DATE type will be converted to specific year, month, and day values before performing the modulus calculation.

- When either value in expr1 or expr2 is NULL, the function will return NULL.

- The BLOB and TEXT type are not allowed to calculate with this function.

- In YashanDB (mysql mode), the MOD function allows the divisor to be 0, and returns 0 in this case.

***Example*** for Heap tables

```sql
SELECT MOD(7,2) mod1,
MOD(10.97,1.23) mod2,
MOD('4.55',2) mod3
FROM DUAL;
mod1        mod2        mod3 
------------ ----------- ----------- 
           1        1.13    5.5E-001
```
