```ebnf
char = CHAR "(" expr1 ["," expr2]")".
```

The CHAR function converts one or more ASCII values represented by [expr](../General SQL Syntax/expr) into corresponding characters, returning the result as VARCHAR type.

**expr**

A general expression whose value must be numeric or can be converted to NUMBER type from character type.

*   If the value of expr is a decimal number, the function will first round it to the nearest integer; if it is a character type, it will be rounded down.
*   The ASCII value range is [0, 255]. When the value of expr is less than 0, the function uses its two's complement as the parameter; when the value of expr is greater than 255, the function converts it into multiple bytes before conversion.
*   If the value of expr is NULL, the function skips that expr.

***Example*** for Standalone Deployment Heap tables

```sql
-- Suppose expr is character type data '42.9'
-- 1. The function will first convert it to a number and round down, resulting in 42
-- 2. Finally, execute the ASCII value conversion
SELECT CHAR('42.9') res FROM DUAL;
res   
----- 
*   

-- Suppose expr is numeric type 83, 73, 67, 83
-- Converts them to corresponding ASCII characters S, I, C, S
SELECT CHAR(83,73,67,83) res FROM DUAL;
res
----- 
SICS

```
