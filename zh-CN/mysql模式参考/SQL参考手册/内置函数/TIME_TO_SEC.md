```ebnf+diagram
time_to_sec::= TIME_TO_SEC "("time")"
```

TIME_TO_SEC函数将time参数指定的时间转换为秒值并返回，time参数为可转换为TIME的数据类型。

示例（HEAP表）

```sql
SELECT TIME_TO_SEC('2025-8-1 13:24:56') res FROM DUAL;

res               
----------------- 
48296
```
