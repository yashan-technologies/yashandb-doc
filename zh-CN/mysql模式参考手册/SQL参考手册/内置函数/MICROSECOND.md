```ebnf
microsecond = MICROSECOND "(" expr ")" .
```

MICROSECOND函数用于计算[expr](../通用SQL语法/expr)的毫秒信息，返回一个BIGINT类型的数值。

**expr**

expr的值须为TIME类型或可以转换为TIME类型的其他类型。

当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT MICROSECOND('2022-10-28 23:59:59.999999999') res FROM DUAL;
                  res
---------------------
                    0

SELECT MICROSECOND('2022-10-28 23:59:59.999999') res FROM DUAL;
                  res
---------------------
               999999
               
```
