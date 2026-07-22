```ebnf
OCTET_LENGTH = OCTET_LENGTH "(" expr ")" .
```

The OCTET_LENGTH function counts the length of [expr](../General SQL Syntax/expr) in bytes and returns a BIGINT value.

This function is equivalent to the [LENGTHB](LENGTH LENGTHB) function.

**expr**

A general expression, whose value must be of character type or another type that can be converted to character type.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3,
       CHARACTER_LENGTH('aabbccDDee') r4,OCTET_LENGTH('aabbccDDee') r5,BIT_LENGTH('aabbccDDee') r6,
       CHAR_LENGTH(null) r7,OCTET_LENGTH(null) r8,BIT_LENGTH(null) r9
FROM DUAL;
   R1   R2    R3    R4    R5    R6    R7    R8    R9
----- ---- ----- ----- ----- ----- ----- ----- -----
    2    6    48    10    10    80
```
