```ebnf+diagram
monthname::= MONTHNAME "("date")"
```

MONTHNAME函数返回date指定日期所在月份的名称。

示例（HEAP表）

```sql
SELECT MONTHNAME('2025-8-1') res FROM DUAL;

res               
----------------- 
August
```
