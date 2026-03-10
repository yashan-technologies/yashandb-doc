```ebnf+diagram
length::= LENGTH "(" expr ")"
```

The LENGTH function calculates the length of the value of [expr](../General SQL Syntax/expr) and returns a BIGINT value.

LENGTHB counts the length in bytes and is synonymous with the [OCTET_LENGTH](OCTET_LENGTH) function. For Chinese characters, different character set environments may return different results. For example, in the UTF8 character set environment, a Chinese character occupies 3 bytes, while in the GBK character set environment, a Chinese character occupies 2 bytes.

**expr**

The value of expr must be of character type or another type convertible to character type, except for BIT type.

- In the vectorized execution engine, expr cannot be LOB type external storage data.

- When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
-- UTF8 character set environment
SELECT LENGTH('——') r1, LENGTH('aabbccDDee') r2, LENGTH(null) r3 FROM DUAL;
                   r1                    r2                    r3
--------------------- --------------------- ---------------------
                    6                    10
    
-- GBK character set environment
SELECT LENGTH('——') r1 FROM DUAL;
                   r1
---------------------
                    4
```
