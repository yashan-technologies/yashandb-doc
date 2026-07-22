```ebnf
timestamp = TIMESTAMP "("timstamp_expr ["," time_expr] ")".
```

如果是1个参数，TIMESTAMP函数计算timestamp_expr表示的日期，返回一个TIMESTAMP类型的日期值。

如果是2个参数，TIMESTAMP函数计算timestamp_expr表示的日期加上time_expr表示的时间，返回一个TIMESTAMP类型的日期值。

**timestamp_expr**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，timestamp_expr的值必须为DATE、TIMESTAMP、TIME或者字符型数据，当为字符型时，必须确保字符串符合当前TIMESTAMP类型的格式要求。

当timestamp_expr为NULL时，函数返回NULL。

**time_expr**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，time_expr的值必须为DATE、TIMESTAMP、TIME或者字符型数据，当为字符型时，必须确保字符串符合当前TIME类型的格式要求。

当time_expr为NULL时，函数返回NULL。

示例

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
RESULT
--------------------------------
2021-5-31 11:11:11.000000

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
RESULT
--------------------------------
2022-1-30 10:10:10.000000
```
