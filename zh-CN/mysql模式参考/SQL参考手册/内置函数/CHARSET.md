```ebnf+diagram
charset::= CHARSET "(" expr ")"
```

CHARSET函数返回给定参数[expr](../通用SQL语法/expr)的值的字符集。输入参数的值为非字符串类型时，返回“binary”，输入参数的值为字符串类型时，返回服务端字符集。

示例（单机HEAP表）

```sql
SELECT CHARSET('yashan') res FROM DUAL;
res       
--------- 
utf8mb4  
    
SELECT CHARSET(123) res FROM DUAL;
res       
--------- 
binary 
```
