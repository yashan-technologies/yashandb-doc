```ebnf+diagram
unsupport_error::= UNSUPPORT_ERROR "("")"
```

UNSUPPORT_ERROR函数返回报错信息 This statement is temporarily unable to execute due to an internal error。

以下两种形式的UNSUPPORT_ERROR同义，返回结果无差别，括号内不允许有参数：

- `UNSUPPORT_ERROR`
- `UNSUPPORT_ERROR()`

示例

```sql
SELECT UNSUPPORT_ERROR() FROM DUAL;

[1:8]YAS-04349 This statement is temporarily unable to execute due to an internal error
```
