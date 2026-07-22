```ebnf
datediff = DATEDIFF "(" expr1 "," expr2 ")" .
```

DATEDIFF() 返回 expr1 - expr2 从一个日期到另一个日期的天数。计算中只使用数值的日期部分。

**expr1/expr2**

- expr1/expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr.md)，必须可转换为DATE类型或者DATETIME类型。

示例（单机HEAP表）

```sql
SQL> select datediff('2021-1-1', '2020-10-30') res;

res
---------------------------------- 
                                63

1 row fetched.

```
