```ebnf
right = RIGHT "(" expr "," length ")".
```

The RIGHT function extracts a substring from the right side of the string represented by [expr](../General SQL Syntax/expr) for the specified length and returns it.

**expr**

The value of expr must be of character type or other types that can be implicitly converted to character type (LOB, BFILE types support implicit conversion).

- In the vectorized execution engine, expr cannot be LOB type external stored data.

- expr cannot exceed 65534 bytes for BFILE or LOB type data.

- When expr is of NCLOB/NCHAR/NVARCHAR type, the return value will be NVARCHAR. In other scenarios, the return value will be VARCHAR.

- When the value of expr is NULL, the function returns NULL.

**length**

Specifies the length of the string to be extracted. Length is a generic expression that must be numeric data like expr, excluding BIT type data, or other types that can be converted to NUMBER type, with a value range of [-2147483648, 2147483647].

- When the value of length is NULL, 0, or negative, the function returns NULL.

- When the value of length is a decimal, the function will first round it, with the following rules:

  - When length is of NUMBER or floating type: round to the nearest integer.

  - When length is of another type convertible to NUMBER: cut off the decimal part.

- If the length value is greater than the length of the expr string, it will be treated as the length of the expr string.

***Example***

```sql
SELECT RIGHT(SYSDATE+1,4.99) res FROM DUAL;
RES                                            
----------------------------------------------------------------
54:23 
 
SELECT RIGHT(SYSDATE+1,'4.99') res FROM DUAL;
RES                                            
----------------------------------------------------------------
3:24
```
