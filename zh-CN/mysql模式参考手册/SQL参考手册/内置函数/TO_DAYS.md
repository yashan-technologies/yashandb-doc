```ebnf
to_days = TO_DAYS "("date")".
```

TO_DAYS函数返回date指定日期与日期'0-1-0'之间的天数差，date参数为可转换为TIMESTAMP的数据类型。

示例（单机HEAP表）

```sql
SELECT TO_DAYS('2025-8-1') res FROM DUAL;

res               
----------------- 
739830
```
