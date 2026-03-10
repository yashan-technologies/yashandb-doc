```ebnf+diagram
collation::= CRC32 "(" expr ")"
```

CRC32函数返回给定参数[expr](../通用SQL语法/expr)的循环冗余校验值，是一个32位无符号数。

**expr**

- expr的类型为字符类型，或可转换为字符类型。

- expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT CRC32('yashan') res FROM DUAL;
res                   
--------------------- 
3491700740
    
SELECT CRC32(123) res FROM DUAL;
res       
--------- 
2286445522 
```