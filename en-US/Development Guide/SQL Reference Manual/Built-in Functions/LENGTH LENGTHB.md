```ebnf
length = LENGTH "(" expr ")".

lengthb = LENGTHB "(" expr ")".
```

The LENGTH/LENGTHB function counts the length of the value of [expr](../General SQL Syntax/expr) and returns a BIGINT value.

- LENGTH counts the length by characters, which is synonymous with the [CHAR_LENGTH and CHARACTER_LENGTH](CHAR_LENGTH CHARACTER_LENGTH) functions.

- LENGTHB counts the length by bytes, which is synonymous with the [OCTET_LENGTH](OCTET_LENGTH) function. For Chinese characters, different character set environments may return different results. For example, in a UTF8 character set environment, one Chinese character occupies 3 bytes, while in a GBK character set environment, one Chinese character occupies 2 bytes.

**expr**

The value of expr must be of string type or other types that can be converted to string, excluding BIT.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
-- In a UTF8 character set environment
SELECT LENGTH('——') r1,LENGTHB('——') r2,
LENGTH('aabbccDDee') r3,LENGTHB('aabbccDDee') r4,
LENGTH(null) r5,LENGTHB(null) r6
FROM DUAL;
   R1   R2    R3    R4    R5    R6
----- ---- ----- ----- ----- -----
    2    6    10    10
    
-- In a GBK character set environment
SELECT LENGTH('——') r1,LENGTHB('——') r2
FROM DUAL;
                   R1                    R2 
--------------------- --------------------- 
                    2                     4
```
