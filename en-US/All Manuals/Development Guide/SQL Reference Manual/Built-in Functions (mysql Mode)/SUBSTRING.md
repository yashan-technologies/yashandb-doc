```ebnf+diagram
substring::= SUBSTRING "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")"
```

The SUBSTRING function returns a substring of length len starting from position pos of the string expr.

**expr**

expr specifies the source string, and its value must be of character type or other types that can be converted to character type.

**pos, len**

pos specifies the starting position, and len specifies the character length. Both must be of BIGINT type or other types that can be converted to BIGINT type.

If the len parameter is not specified or the value of len exceeds the actual length that can be extracted, it will extract the maximum length substring starting from pos.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SUBSTRING('abcdef', 3);

substring('abcdef', 3) 
---------------------- 
cdef

SELECT SUBSTRING('abcdef' FROM 2);

substring('abcdef' from 2) 
-------------------------- 
bcdef

SELECT SUBSTRING('abcdefgh', 2, 6);

substring('abcdefgh', 2, 6) 
--------------------------- 
bcdefg

SELECT SUBSTRING('abcdefgh' FROM 2 FOR 5);

substring('abcdefgh' from 2 for 5) 
---------------------------------- 
bcdef
```
