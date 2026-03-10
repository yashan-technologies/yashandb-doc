```ebnf+diagram
bit_length::= BIT_LENGTH "(" expr ")" 
```

BIT_LENGTH函数按比特位统计[expr](../通用SQL语法/expr)的长度，返回一个BIGINT的数值。

**expr**

expr的值须为字符型、BIT类型或除浮点型和可转化为字符型的其他类型。

- 当expr为字符型或转换后为字符型时，函数统计所有字符所占比特位之和。对于中文字符，其比特位长度与数据库服务端字符集类型相关。例如，在UTF8字符集环境中，一个中文字符占24比特位，而在GBK字符集环境中，一个中文字符占16比特位。
- 当expr为BIT类型时，函数统计该BIT数字所占的比特位之和。
- 当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
-- UTF8字符集环境
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                   r1                    r2                    r3
--------------------- --------------------- ---------------------
                    2                     6                    48
    
-- GBK字符集环境
SELECT CHAR_LENGTH('——') r1,OCTET_LENGTH('——') r2,BIT_LENGTH('——') r3
FROM DUAL;
                    r1                    r2                    r3
-------------------- --------------------- --------------------- 
                   2                     4                    32

-- BIT数据
SELECT BIT_LENGTH(b'101') rb, BIT_LENGTH('101') rc, BIT_LENGTH(101) rn
FROM DUAL;
                   rb                    rc                    rn 
--------------------- --------------------- --------------------- 
                    8                    24                    24
```
