```ebnf+diagram
char_length::= CHAR_LENGTH "(" expr ")" 
character_length::= CHARACTER_LENGTH "(" expr ")" 
```

The CHAR_LENGTH/CHARACTER_LENGTH function counts the number of characters in [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) and returns a BIGINT value. CHAR_LENGTH, CHARACTER_LENGTH, and [LENGTH](LENGTH LENGTHB) functions are synonymous with each other.

**expr**

A generic expression whose value must be of character type or another type that can be converted to a character type.

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
