```ebnf+diagram
yearweek::= YEARWEEK "("date ["," mode] ")"
```

YEARWEEK函数返回date指定日期所在日历周的年份和周数。参数mode的用法等同于[WEEK](./WEEK)函数中mode参数的用法，但YEARWEEK函数的返回值范围总为1-53。

若不指定mode参数，mode值默认为0，YEARWEEK函数中mode默认值不受DEFAULT_WEEK_FORMAT系统变量影响。

示例（单机HEAP表）

```sql
SELECT YEARWEEK('2025-1-1', 2) res FROM DUAL;

res               
----------------- 
202452
```