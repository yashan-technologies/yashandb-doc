```ebnf+diagram
dayofyear::= DAYOFYEAR "(" expr ")" 
```

DAYOFYEAR函数用于计算[expr](../通用SQL语法/expr)位于所在年的第几天，返回一个INT类型的数值。

**expr**

expr的值须为TIMESTAMP类型或可以转换为TIMESTAMP类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT DAYOFYEAR('01-07-26 1:1:1.123') res;
         res
------------
         207 

SELECT DAYOFYEAR('2022-10-28 23:59:59.999999999') res;
         res
------------
         302
```
