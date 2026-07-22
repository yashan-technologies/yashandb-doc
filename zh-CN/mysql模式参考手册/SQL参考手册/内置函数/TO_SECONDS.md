```ebnf
to_seconds = TO_SECONDS "("date")".
```

TO_SECONDS函数返回date指定日期与日期'0-1-0'之间的秒数差，date参数为可转换为TIMESTAMP的数据类型。

示例（HEAP表）

```sql
SELECT TO_SECONDS('2025-8-1') res FROM DUAL;

res               
----------------- 
63921312000
```
