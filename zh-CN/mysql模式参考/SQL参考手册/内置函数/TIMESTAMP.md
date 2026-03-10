```ebnf+diagram
timestamp::= TIMESTAMP "("timstamp_expr ["," time_expr] ")"
```

如果是1个参数，TIMESTAMP函数将timestamp_expr当作TIMESTAMP类型，并返回该参数表示的TIMESTAMP值，返回值为TIMESTAMP类型。

如果是2个参数，TIMESTAMP函数将timestamp_expr当作TIMESTAMP类型、将time_expr当作TIME类型，返回两个参数之和，返回值为TIMESTAMP类型。

**timestamp_expr**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，必须可转换为TIMESTAMP类型。

当timestamp_expr的值为字符型时，其格式必须符合以下规范：

*   字符型1：字符串可被转换为数值且所得数值的整数位数大于等于5。
*   字符型2：'yyyy-mm-dd'格式的字符串，需符合年、月、日的限制条件，即年值介于0-9999之间、月份值介于1-12之间、日期值介于1-31之间，TIMESTAMP函数会将小时、分钟、秒值补位0。
*   字符型3：'yyyy-mm-dd hh24:mi:ss.ff'格式的字符串，需符合年、月、日、时、分、秒的限制条件，即年值介于0-9999之间、月份值介于1-12之间、日期值介于1-31之间、小时值介于0-23之间、分钟值介于0-59之间、秒值介于0-59之间，微秒值至多保留6位小数。

当timestamp_expr为NULL时，函数返回NULL。

**time_expr**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，必须可转换为TIME类型。

当time_expr为NULL且timestamp_expr为合法参数时，函数返回NULL。

示例（HEAP表）

yasql和MySQL客户端对时间格式的回显打印机制不同，不影响业务通过JDBC客户端驱动连接mysql模式的YashanDB使用。

以下为yasql中的查询示例。

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
result
--------------------------------
2021-5-31 11:11:11.000000

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
result
--------------------------------
2022-1-30 10:10:10.000000
```

以下为MySQL Client中的查询示例。

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
+---------------------+
| result              |
+---------------------+
| 2021-05-31 11:11:11 |
+---------------------+

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
+---------------------+
| result              |
+---------------------+
| 2022-01-30 10:10:10 |
+---------------------+
```