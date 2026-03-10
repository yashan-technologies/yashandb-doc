```ebnf+diagram
bit_length::= BIT_LENGTH "(" expr ")" 
```

The BIT_LENGTH function counts the bit length of [expr](../General SQL Syntax/expr) and returns a BIGINT value.

**expr**

The value of expr must be of character type, or other types that can be converted to character type exclude FLOAT type.

- When expr is of character type or converted to character type, the function counts the total number of bits for all characters. The bit length of Chinese characters is related to the character set type of the database server. For example, in a UTF8 character set environment, one Chinese character occupies 24 bits, while in a GBK character set environment, one Chinese character occupies 16 bits.
- When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- UTF8 character set environment
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                   r1                    r2                    r3
--------------------- --------------------- ---------------------
                    2                     6                    48
    
-- GBK character set environment
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                    r1                    r2                    r3
-------------------- --------------------- --------------------- 
                   2                     4                    32

-- BIT data
SELECT BIT_LENGTH(b'101') rb, BIT_LENGTH('101') rc, BIT_LENGTH(101) rn
FROM DUAL;
ERROR 4401 (HY000): YAS-04401 data type CHAR OR STRING expected, but BIT got

```
