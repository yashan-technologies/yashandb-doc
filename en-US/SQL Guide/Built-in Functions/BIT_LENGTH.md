```ebnf+diagram
bit_length::= BIT_LENGTH "(" expr ")" 
```

The BIT_LENGTH function counts the length of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) in bits and returns a BIGINT value.

This function does not support vectorization calculation.

**expr**

The value of expr must be of character type, BIT type, or other types that can be converted to character type, excluding floating point types.

- When expr is of character type or converted to character type, the function counts the total number of bits occupied by all characters. For Chinese characters, the bit length is related to the character set type of the database server. For example, in a UTF8 character set environment, a Chinese character occupies 24 bits, while in a GBK character set environment, a Chinese character occupies 16 bits.
- When expr is of BIT type, the function counts the total number of bits occupied by the BIT number.
- When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- In UTF8 character set environment
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                   R1                    R2                    R3 
--------------------- --------------------- --------------------- 
                    2                     6                    48
    
-- In GBK character set environment
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                   R1                    R2                    R3 
--------------------- --------------------- --------------------- 
                    2                     4                    32
					
-- BIT data
SELECT BIT_LENGTH(b'101') rb, BIT_LENGTH('101') rc, BIT_LENGTH(101) rn
FROM DUAL;
                   RB                    RC                    RN 
--------------------- --------------------- --------------------- 
                    8                    24                    24
```
