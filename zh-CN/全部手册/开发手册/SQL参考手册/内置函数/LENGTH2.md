```ebnf+diagram
length2::= LENGTH2 "(" expr ")"
```
LENGTH2函数统计[expr](../通用SQL语法/expr)的值的长度，支持以UTF16编码计算长度，返回一个BIGINT的数值。

LENGTH2按字符统计长度，与[CHAR_LENGTH和CHARACTER_LENGTH](./CHAR_LENGTH CHARACTER_LENGTH)函数同义。

本函数不支持向量化计算。

**expr**

通用表达式，其值须为字符型或除BIT和LOB类型外的可转化为字符型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
-- UTF8字符集环境
SELECT LENGTH2('——') r1,LENGTH('——') r2,
LENGTH2('aabbccDDee') r3,LENGTH('aabbccDDee') r4,
LENGTH2(null) r5,LENGTH(null) r6
FROM DUAL;
   R1   R2    R3    R4    R5    R6
----- ---- ----- ----- ----- -----
    2    2    10    10
    
-- GBK字符集环境
SELECT LENGTH2('——') r1,LENGTH('——') r2
FROM DUAL;
                   R1                    R2 
--------------------- --------------------- 
                    2                     2

-- LENGTH与LENGTH2的区别                  
SELECT LENGTH(TO_CHAR('😂')) LENGTH FROM DUAL;

               LENGTH
---------------------
                    1

SELECT LENGTH2(TO_CHAR('😂')) LENGTH2 FROM DUAL;

              LENGTH2
---------------------
                    2
```
