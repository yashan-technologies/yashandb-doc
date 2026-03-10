```ebnf+diagram
timestampadd::= TIMESTAMPADD "(" unit "," interval ","  datetime_expr ")" 
```

DATEDIFF()将整数表达式 interval 添加到日期或日期时间表达式 datetime_expr。

**unit**

- 区间的单位由单位参数给出，单位参数应为以下值之一： MICROSECOND（微秒）、SECOND（秒）、MINUTE（分钟）、HOUR（小时）、DAY（天）、WEEK（周）、MONTH（月）、QUARTER（季度）或 YEAR（年）。
- 单位值可以使用如图所示的关键字之一指定，也可以使用 SQL_TSI_ 前缀指定。例如，DAY 和 SQL_TSI_DAY 都是合法的。

**interval**

- unit单位对应的值。

**datetime_expr**

- 日期或日期时间表达式。

示例（HEAP表）

```sql
SELECT TIMESTAMPADD(sql_tsi_year,-1,'2011-1-1') res;

res                         
---------------------------------------------------------------- 
2010-01-01                                                      


```
