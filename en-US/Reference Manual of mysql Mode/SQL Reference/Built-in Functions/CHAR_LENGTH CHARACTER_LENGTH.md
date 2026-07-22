```ebnf
char_length = CHAR_LENGTH "(" expr ")" .
character_length = CHARACTER_LENGTH "(" expr ")" .
```

The CHAR_LENGTH/CHARACTER_LENGTH function counts the length of [expr](../General SQL Syntax/expr) by characters and returns a BIGINT value. The CHAR_LENGTH and CHARACTER_LENGTH functions are synonymous.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3,
       CHARACTER_LENGTH('aabbccDDee') r4,OCTET_LENGTH('aabbccDDee') r5,BIT_LENGTH('aabbccDDee') r6,
       CHAR_LENGTH(null) r7,OCTET_LENGTH(null) r8,BIT_LENGTH(null) r9
FROM DUAL;
    r1      r2        r3         r4         r5          r6        r7          r8         r9
------ ------- --------- ---------- ---------- ----------- --------- ----------- ----------
     2       6        48         10         10          80
```
