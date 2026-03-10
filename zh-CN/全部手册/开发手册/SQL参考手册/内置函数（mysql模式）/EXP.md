```ebnf+diagram
exp::= EXP "(" expr ")"
```

EXP函数计算以e=2.71828183... 为底，[expr](../通用SQL语法/expr)表示的数值为指数的数学结果，返回一个DOUBLE类型的数值。

expr的值须为数值型，可以是数值型字符串，对于其他类型，函数返回类型不支持错误。

当expr为NULL时，返回NULL。

示例（单机HEAP表）

```sql
SET numwidth 30
SELECT EXP(4.444444) res FROM DUAL;
                            RES 
------------------------------- 
        8.5152519871979379E+001
```
