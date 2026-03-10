```ebnf+diagram
length2::= LENGTH2 "(" expr ")"
```
The LENGTH2 function counts the length of the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), supporting length calculation with UTF16 encoding and returning a BIGINT value.

LENGTH2 counts length by characters and is synonymous with the [CHAR_LENGTH and CHARACTER_LENGTH](CHAR_LENGTH CHARACTER_LENGTH) functions.

This function does not support vectorization calculation.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type, excluding BIT, BFILE, and LOB types.

When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- UTF8 character set environment
SELECT LENGTH2('——') r1,LENGTH('——') r2,
LENGTH2('aabbccDDee') r3,LENGTH('aabbccDDee') r4,
LENGTH2(null) r5,LENGTH(null) r6
FROM DUAL;
   R1   R2    R3    R4    R5    R6
----- ---- ----- ----- ----- -----
    2    2    10    10
    
-- GBK character set environment
SELECT LENGTH2('——') r1,LENGTH('——') r2
FROM DUAL;
                   R1                    R2 
--------------------- --------------------- 
                    2                     2

-- Difference between LENGTH and LENGTH2                  
SELECT LENGTH(TO_CHAR('😂')) LENGTH FROM DUAL;

               LENGTH
---------------------
                    1

SELECT LENGTH2(TO_CHAR('😂')) LENGTH2 FROM DUAL;

              LENGTH2
---------------------
                    2
```
