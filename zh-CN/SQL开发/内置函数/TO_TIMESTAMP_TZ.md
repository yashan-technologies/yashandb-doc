```ebnf+diagram
to_timestamp_tz::= TO_TIMESTAMP_TZ "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format] ")"
```

TO_TIMESTAMP_TZ函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值转换为TIMESTAMP WITH TIME ZONE类型数据。

**expr**

expr的值须为字符型或者时间类型，且其内容须符合format格式，否则返回格式转换错误。

当expr的值为NULL时，函数返回NULL。      

**DEFAULT replace_expr ON CONVERSION ERROR**

表示当对expr转换失败时，使用replace_expr值来进行转换，replace_expr为一个字符串字面量，其内容须符合format格式。

示例

```sql
SELECT TO_TIMESTAMP_TZ('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-02-13 00:00:00.000000 +00:00
```

**format**

format用于指定expr的时间格式，其值须为字符型，可省略，省略则默认expr格式同TIMESTAMP WITH TIME ZONE类型的默认格式。

format支持中文年月日，使用时须用双引号包围中文字符，且format中不能指定为可变长度的转换格式，例如MON、MONTH、DAY；expr中的中文字符无须用双引号包围。

如果format中未指定年，则获取当前年份然后填充；如果不存在月，则获取当前月份然后填充；如果不存在日，则用1来填充；未指定时间则用0来填充。

YashanDB支持不含连接字符的expr与format进行匹配，该匹配须遵循如下规则：

- expr中数字会按照format指定的格式进行转换，建议输入相同长度的数字和转换格式，否则可能导致结果错误。
- format中不能指定为可变长度的转换格式，例如MONTH、DAY。

指定转换的格式，包括如下字符组合：

*   年
    * 年份数字：`YYYY`、`Y`、`YY`、`YYY`
*   月
    * 月份数字：`MM`
    * 月份全称：`MONTH`
    * 月份缩写：`MON`
*   日
    * 日期数字：`DD`
*   日期描述
    * 一周中的一天全称（SUNDAY-SATURDAY）：`DAY`
    * 一周中的一天数字（1-7）：`D`
    * 一年中的一天（1-366）：`DDD`
*   时
    * 24小时制小时：`HH24`
    * 12小时制小时：`HH`、`HH12`
*   分
    * 分钟数：`MI`
*   秒
    * 秒数：`SS`
* 儒略日
    * 儒略日计数：`J`
*   连接字符：`:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_`、 ` ` 、 `[`、 `]`
    * `:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_` 、 ` ` 、 `[`、 `]`：连接符之间可以相互匹配
    * ` `：空格可不参与匹配
* 小数点：'X'
* 时区小时
    * 时区小时：`TZH`
* 时区分钟
    * 时区分钟：`TZM`

示例

```sql
SELECT TO_TIMESTAMP_TZ('2022,JUL:02 08,00.00.0 +8', 'yyyy,MON:dd hh24,mi.ss.ff TZH.TZM') RES FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2022-07-02 08:00:00.000000 +08:00
```

**nls_calendar**

指定数据库使用的日历系统，仅支持为字符串类型，默认值为gregorian。

目前YashanDB仅支持gregorian日历系统。

示例

```sql
SELECT TO_TIMESTAMP_TZ('2020010102','YYYYMMDDHH', 'nls_calendar = gregorian') res FROM DUAL;
RES
----------------------------------------------------------------
2020-01-01 02:00:00.000000 +00:00

```
