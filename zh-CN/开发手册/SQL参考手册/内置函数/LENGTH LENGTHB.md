```ebnf
length = LENGTH "(" expr ")".

lengthb = LENGTHB "(" expr ")".
```

LENGTH/LENGTHB函数统计[expr](../通用SQL语法/expr)的值的长度，返回一个BIGINT的数值。

- LENGTH按字符统计长度，与[CHAR_LENGTH和CHARACTER_LENGTH](./CHAR_LENGTH CHARACTER_LENGTH)函数同义。

- LENGTHB按字节统计长度，与[OCTET_LENGTH](./OCTET_LENGTH)函数同义。对于中文字符，不同的字符集环境可能返回不同的结果，例如，在UTF8字符集环境中，一个中文字符占3字节，而在GBK字符集环境中，一个中文字符占2字节。

**expr**

expr的值须为字符型或除BIT和可转化为字符型的其他类型。

- 当expr的值为NULL时，函数返回NULL。

示例

```sql
-- UTF8字符集环境
SELECT LENGTH('——') r1,LENGTHB('——') r2,
LENGTH('aabbccDDee') r3,LENGTHB('aabbccDDee') r4,
LENGTH(null) r5,LENGTHB(null) r6
FROM DUAL;
   R1   R2    R3    R4    R5    R6
----- ---- ----- ----- ----- -----
    2    6    10    10
    
-- GBK字符集环境
SELECT LENGTH('——') r1,LENGTHB('——') r2
FROM DUAL;
                   R1                    R2 
--------------------- --------------------- 
                    2                     4
```
