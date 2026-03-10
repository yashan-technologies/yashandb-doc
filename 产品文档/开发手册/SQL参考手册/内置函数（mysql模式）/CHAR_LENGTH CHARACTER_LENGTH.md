```ebnf+diagram
char_length::= CHAR_LENGTH "(" expr ")" 
character_length::= CHARACTER_LENGTH "(" expr ")" 
```

CHAR_LENGTH/CHARACTER_LENGTH函数按字符统计[expr](../通用SQL语法/expr)的长度，返回一个BIGINT的数值。CHAR_LENGTH、CHARACTER_LENGTH函数同义。


**expr**

通用表达式，其值须为字符型或可转化为字符型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT CHAR_LENGTH('深圳') r1,OCTET_LENGTH('深圳') r2,BIT_LENGTH('深圳') r3,
       CHARACTER_LENGTH('aabbccDDee') r4,OCTET_LENGTH('aabbccDDee') r5,BIT_LENGTH('aabbccDDee') r6,
       CHAR_LENGTH(null) r7,OCTET_LENGTH(null) r8,BIT_LENGTH(null) r9
FROM DUAL;
   R1   R2    R3    R4    R5    R6    R7    R8    R9
----- ---- ----- ----- ----- ----- ----- ----- -----
    2    6    48    10    10    80
```

