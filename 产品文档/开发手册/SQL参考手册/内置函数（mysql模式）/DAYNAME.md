```ebnf+diagram
dayname::= DAYNAME "("date")"
```

DAYNAME函数返回date指定日期的星期几的名称。

示例（单机HEAP表）

```sql
SELECT DAYNAME('2025-8-1') res FROM DUAL;

res               
----------------- 
Friday
```