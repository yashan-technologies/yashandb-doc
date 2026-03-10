```ebnf+diagram
add_months::= ADD_MONTHS "(" date_expr "," integer_expr ")"
```

ADD_MONTHS函数计算date_expr表示的日期加上integer_expr表示的月数（可以为负数），返回一个DATE类型的日期值。

**date_expr**

YashanDB认可的[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，date_expr的值必须为DATE、TIMESTAMP或者字符型数据。

- 当date_expr为字符型时，必须确保字符串符合当前DATE类型的格式要求，否则返回错误。

- 当date_expr为NULL时，函数返回NULL。

**integer_expr**

YashanDB认可的[通用表达式](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，integer_expr的值必须为NUMBER类型或可转换为NUMBER的字符型。

integer_expr的值存在小数时：

- 对于浮点数（FLOAT和DOUBLE），奇进偶舍至整数。
- 对于非浮点数，直接将小数舍去，留下整数部分。

**天的特殊处理**

由于存在大小月的情况，对天做如下特殊处理：

* 当date_expr的DAY值为所在月最后一天时，函数返回日期的DAY值也为其所在月的最后一天。
* 当date_expr的DAY值大于加上integer_expr后所得月份的最后一天时，函数返回日期的DAY值为该月份的最后一天。

示例

```sql
SELECT ADD_MONTHS(DATE'2021-5-31',-3) res FROM DUAL;
RES
--------------------------------
2021-02-28

SELECT ADD_MONTHS(DATE'2022-1-30',1) res FROM DUAL;
RES
--------------------------------
2022-02-28
```
