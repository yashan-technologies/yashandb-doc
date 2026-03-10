```ebnf+diagram
minute::= MINUTE "(" expr ")" 
```

MINUTE函数用于计算[expr](../通用SQL语法/expr)的分钟信息，返回一个INT类型的数值。

**expr**

expr的值须为TIME型或可以转换为TIME类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT MINUTE('10:17:26') res;
         RES
------------
          17 

SELECT MINUTE('2022-10-28 23:59:59.111') res;
         RES
------------
          59
```

