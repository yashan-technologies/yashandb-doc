```ebnf+diagram
tz_offset::= TZ_OFFSET "(" expr ")"
```

TZ_OFFSET函数基于语句执行的日期，返回入参相对UTC的时区偏移。

**expr**

支持两类输入：

- 相对于UTC的时区偏移，该输入将返回它本身。可以为任意类型，转换为字符串类型后不符合时区格式则报错。

- 关键字 SESSIONTIMEZONE 或者 DBTIMEZONE。

示例

```sql
SELECT TZ_OFFSET('+08:00') res FROM DUAL;
RES
--------------------
+08:00
```
