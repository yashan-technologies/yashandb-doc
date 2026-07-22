```ebnf
last_day = LAST_DAY "(" expr ")".
```

LAST\_DAY函数返回[expr](../通用SQL语法/expr)表示的日期所在月份的最后一天的日期值，返回类型为DATE。

**expr**

expr的值须为或可转为DATE/TIMESTAMP/TIME的数据类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT LAST_DAY('2024-02-01') res FROM DUAL;
res            
--------------------------------
2024-02-29
```
