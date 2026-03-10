```ebnf+diagram
hour::= HOUR "(" expr ")" 
```

HOUR函数用于计算[expr](../通用SQL语法/expr)的小时信息，返回一个INT类型的数值。

**expr**

expr的值须为TIME类型或可以转换为TIME类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT HOUR('01:07:26.123') res;
         RES
------------
           1 

SELECT HOUR('2022-10-28 23:59:59.999999999') res;
         RES
------------
           24

```

