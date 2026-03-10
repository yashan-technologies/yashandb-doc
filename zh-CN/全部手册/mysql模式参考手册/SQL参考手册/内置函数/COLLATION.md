```ebnf+diagram
collation::= COLLATION "(" expr ")"
```

COLLATION函数返回给定参数[expr](../通用SQL语法/expr)的值的字符序。输入参数的值为非字符串类型时，返回“binary”，输入参数的值为字符串类型时，返回服务端字符集默认的字符序。

示例（HEAP表）

```sql
SELECT COLLATION('yashan') res FROM DUAL;
res                   
--------------------- 
utf8mb4_general_ci
    
SELECT COLLATION(123) res FROM DUAL;
res       
--------- 
binary 
```
