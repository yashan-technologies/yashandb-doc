```ebnf+diagram
year::= YEAR "(" expr ")" 
```

YEAR函数用于计算[expr](../通用SQL语法/expr)的年份信息，返回一个INT类型的数值。

**expr**

expr的值须为TIMESTAMP类型或可以转换为TIMESTAMP类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT YEAR('2022-7-26') res;
         res
------------
        2022 

SELECT YEAR('00-10-28 23:59:59.999999999') res FROM DUAL;
         res
------------
        2000
```
