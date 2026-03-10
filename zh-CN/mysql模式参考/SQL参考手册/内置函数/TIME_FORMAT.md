```ebnf+diagram
time_format::= TIME_FORMAT "(" time "," format ")" 
```

TIME_FORMAT函数根据format参数中指定的格式字符串提取time参数指定的TIME类型数据，并返回得到的字符串。format的合法值等同于[DATE_FOMRAT](./DATE_FORMAT)函数中的format参数，但TIME_FORMAT函数除时、分、秒、微秒的格式外，其它格式均返回NULL或0。

当time参数中的hour大于23时，%H、%k格式的返回值可以大于23，而其它hour相关的格式返回值为hour对12的余数。

示例（HEAP表）

```sql
SELECT TIME_FORMAT('2025-8-1 23:12:56', '%H %k %h %I %l') res FROM DUAL;

res               
----------------- 
23 23 11 11 11
```
