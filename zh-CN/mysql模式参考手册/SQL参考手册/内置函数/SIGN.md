```ebnf
sign = SIGN "(" expr ")".
```

SIGN函数返回[expr](../通用SQL语法/expr)表示的数值的符号，正数返回1、负数返回-1、0返回0，返回值为BIGINT类型。

**expr**

[通用表达式](../通用SQL语法/expr)，其值须为数值型或可转换为数值型的其他类型。

- 当expr的值为日期类型时，将日期类型转换为具体的年月日数值后进行判断数值符号计算。

- 当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT sign(7.11) sign1,
       sign(-10.97) sign2,
       sign(0) sign3
FROM DUAL;
sign1                 sign2                 sign3 
--------------------- --------------------- --------------------- 
                    1                    -1                     0
```
