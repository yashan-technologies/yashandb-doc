```ebnf+diagram
day::= DAY "(" expr ")" 
dayofmonth::= DAYOFMONTH "(" expr ")" 
```

DAY/DAYOFMONTH函数用于计算[expr](../通用SQL语法/expr)位于所在月的第几天，返回一个INT类型的数值。

**expr**

expr的值须为TIMESTAMP类型或可以转换为TIMESTAMP类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT DAYOFMONTH('2022-7-26') res;
         RES
------------
          26 

SELECT DAY('2022-10-28 23:59:59.999999999') res FROM DUAL;
         RES
------------
           7

SELECT DAY('2022-10-28 23:59:59.99999901') res FROM DUAL;
         RES
------------
           6
```
