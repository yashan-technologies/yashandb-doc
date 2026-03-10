```ebnf+diagram
upper::= UPPER "(" expr ")"
```

The UPPER function is used to convert lowercase letters in the source string [expr](../General SQL Syntax/expr) to uppercase letters, returning a new string that has been converted.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type.

- In the vectorized execution engine, expr cannot be LOB type external stored data.

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- When expr is of CHAR, NCHAR, or NVARCHAR type, the return type is the same as that of expr. In other cases, the return type is VARCHAR.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT UPPER('Shenzhen nihao') l1, UPPER(NULL) l2 FROM DUAL;
L1                L2
----------------- -----
SHENZHEN NIHAO
```
