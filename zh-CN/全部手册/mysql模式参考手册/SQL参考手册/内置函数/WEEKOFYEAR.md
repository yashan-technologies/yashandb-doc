```ebnf+diagram
weekofyear::= WEEKOFYEAR "("date")"
```

WEEKOFYEAR函数返回date指定日期所在日历周的周数，返回值范围为1-53。WEEKOFYEAR函数兼容等价于[WEEK](./WEEK)(date, 3)。

示例（单机HEAP表）

```sql
SELECT WEEKOFYEAR('2025-8-1') res FROM DUAL;

res               
----------------- 
31
```
