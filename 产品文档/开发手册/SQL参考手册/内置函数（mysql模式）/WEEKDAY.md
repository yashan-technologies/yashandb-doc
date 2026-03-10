```ebnf+diagram
weekday::= WEEKDAY "("date")"
```

MONTHNAME函数返回date指定日期的星期几的索引，0表示周一，1表示周二，6表示周日，以此类推。

示例（单机HEAP表）

```sql
SELECT WEEKDAY('2025-8-1') res FROM DUAL;

res               
----------------- 
4
```