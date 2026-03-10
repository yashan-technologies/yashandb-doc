```ebnf+diagram
date_sub::= DATE_SUB "(" expr "," INTERVAL interval_value interval_unit ")"
```

DATE_SUB函数用于执行日期运算，通过[expr](../通用SQL语法/expr)的值加上给定的区间值得到时间推进或后退过的结果。

**expr**

通用表达式，expr的值须为DATE、TIME、TIMESTAMP类型或可以转换为DATE、TIMESTAMP类型的字符型。

- 当expr为字面量时，只能为DATE和TIMESTAMP关键字的输入字符串，不能为TIME关键字的输入字符串。例如，DATE '2012-10-12'、TIMESTAMP '2012-10-12 10:20:24.000006'为函数可接受的expr值，而当expr为TIME '10:20:24'时函数则返回错误。

- 当expr为NULL时，函数返回NULL。

**interval_value**

interva_value为通用表达式expr，用于指定指定时间前进或后退的区间值。

**interval_unit**

指定区间值的单位，该值不可为NULL，单位和对应value的格式对应关系如下：

| interval_unit       | interval_format                           |
|---------------------|-------------------------------------------|
| MICROSECOND         | MICROSECONDS                              |
| SECOND              | SECONDS                                   |
| MINUTE              | MINUTES                                   |
| HOUR                | HOURS                                     |
| DAY                 | DAYS                                      |
| WEEK                | WEEKS                                     | 
| MONTH               | MONTHS                                    |
| QUARTER             | QUARTERS                                  |
| YEAR                | YEARS                                     |
| SECOND_MICROSECOND	 | 'SECONDS.MICROSECONDS'                    |
| MINUTE_MICROSECOND  | 'MINUTES:SECONDS.MICROSECONDS'            |
| MINUTE_SECOND       | 'MINUTES:SECONDS'                         |
| HOUR_MICROSECOND    | 'HOURS:MINUTES:SECONDS.MICROSECONDS'      |
| HOUR_SECOND	        | 'HOURS:MINUTES:SECONDS'                   |
| HOUR_MINUTE         | 'HOURS:MINUTES'                           |
| DAY_MICROSECOND     | 'DAYS HOURS:MINUTES:SECONDS.MICROSECONDS' |
| DAY_SECOND          | 'DAYS HOURS:MINUTES:SECONDS'              |
| DAY_MINUTE          | 'DAYS HOURS:MINUTES'                      |
| DAY_HOUR            | 'DAYS HOURS'                              |
| YEAR_MONTH          | 'YEARS-MONTHS'                            |

如果interval_value中数字的个数小于interval_unit中的个数，将优先匹配较小的时间单位，例如INTERVAL '1:1' HOUR_SECOND表示一分零一秒，默认视作小时单位对应的增量为0。

**日期运算规则**

- 当expr为DATE类型的数据时，若interval_unit中的最小单位涉及时分秒微秒则函数返回值为TIMESTAMP类型，否则函数返回值为DATE类型。

- 当expr为TIMESTAMP类型的数据时，函数返回值为TIMESTAMP类型。

- 当expr为TIME类型的数据时，函数返回值为TIME类型。

- 当expr为其它类型的数据时，函数返回值为VARCHAR类型。


示例（单机HEAP表）

```sql
SELECT DATE_SUB('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;

res
---------------------------------
2013-10-12


```
