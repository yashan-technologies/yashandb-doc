```ebnf+diagram
quarter::= QUARTER "("date")"
```

QUARTER函数返回date指定日期所在的季度，返回值范围为1-4。

示例（HEAP表）

```sql
SELECT QUARTER('2025-8-1') res FROM DUAL;

res               
----------------- 
3
```
