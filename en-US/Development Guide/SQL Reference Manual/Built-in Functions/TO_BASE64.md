```ebnf
to_base64 = TO_BASE64 "(" expr ")".
```

The TO_BASE64 function encodes the string represented by [expr](../General SQL Syntax/expr) using BASE64 and returns the encoded VARCHAR type string data.

This function does not support vectorization calculation.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type (LOB types support implicit conversion).

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT TO_BASE64('ABC') RES FROM DUAL;

RES   
----- 
QUJD 
```
