```ebnf+diagram
length::= LENGTH "(" expr ")"
```

LENGTH函数统计[expr](../通用SQL语法/expr)的值的长度，返回一个BIGINT的数值。

LENGTHB按字节统计长度，与[OCTET_LENGTH](OCTET_LENGTH)函数同义。对于中文字符，不同的字符集环境可能返回不同的结果，例如，在UTF8字符集环境中，一个中文字符占3字节，而在GBK字符集环境中，一个中文字符占2字节。

**expr**

expr的值须为字符型或除BIT类型外的可转化为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- 当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
-- UTF8字符集环境
SELECT LENGTH('——') r1, LENGTH('aabbccDDee') r2, LENGTH(null) r3 FROM DUAL;
                   r1                    r2                    r3
--------------------- --------------------- ---------------------
                    6                    10
    
-- GBK字符集环境
SELECT LENGTH('——') r1 FROM DUAL;
                   r1
---------------------
                    4
```
