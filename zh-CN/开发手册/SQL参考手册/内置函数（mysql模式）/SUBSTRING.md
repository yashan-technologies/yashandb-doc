```ebnf+diagram
substring::= SUBSTRING "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")"
```

SUBSTRING函数返回字符串[expr](../通用SQL语法/expr)的第pos位开始的len个字符的子串。

**expr**

expr指定源字符串，其值须为字符型或可转换为字符型的其他类型。

**pos, len**

pos指定开始位置，len指定字符长度，二者均须为BIGINT类型、可转换为BIGINT类型的其他类型。

若不指定len参数或len参数值超过实际能截取长度，则截取从pos开始的最大长度子串。

示例（单机HEAP表）

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
