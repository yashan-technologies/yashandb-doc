```ebnf+diagram
to_timestamp::= TO_TIMESTAMP "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format] ")"
```

TO_TIMESTAMP函数将[expr](../通用SQL语法/expr)的值转换为TIMESTAMP类型数据，返回值格式为TIMESTAMP类型的默认格式。

**expr**

expr的值须为字符型或者时间类型，且其内容须符合format格式，否则返回格式转换错误。

当expr的值为NULL时，函数返回NULL。      

**DEFAULT replace_expr ON CONVERSION ERROR**

表示当对expr转换失败时，使用replace_expr值来进行转换，replace_expr为一个字符串字面量，其内容须符合format格式。

示例

```sql
SELECT TO_TIMESTAMP('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-02-13 00:00:00.000000
```

**format**

format用于指定expr的时间格式，其值须为字符型，可省略，省略则默认expr格式同TIMESTAMP类型的默认格式。

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

> **Note**: 
>
> 日期描述类格式须与年月日格式同时指定，且expr中日期描述须与年月日匹配，否则返回错误。

其中对于年格式的指定存在如下转换规则：

| expr\\format | Y   | YY  | YYY | YYYY |
| --- | --- | --- | --- | --- |
| 1   | 2021-06-01 | 2001-06-01 | 2001-06-01 | 0001-06-01 |
| 12  | 报错  | 2012-06-01 | 2012-06-01 | 0012-06-01 |
| 123 | 报错  | 0123-06-01 | 2123-06-01 | 0123-06-01 |
| 1234 | 报错  | 1234-06-01 | 报错  | 1234-06-01 |

示例

```sql
SELECT TO_TIMESTAMP('2','hh') timestamp1,TO_TIMESTAMP(TO_CHAR(SYSDATE)) timestamp2 FROM DUAL;
TIMESTAMP1                   TIMESTAMP2                  
---------------------------- ----------------------------
2022-01-01 02:00:00.000000   2022-01-09 00:00:00.000000

SELECT TO_TIMESTAMP('2020-01-01-02','YYYY-MM-DD-HH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000                                      

SELECT TO_TIMESTAMP( '2020-01-01,1','YYYY-MM-DD,DDD' ) res FROM DUAL;
RES
-------------------------------- 
2020-01-01 00:00:00.000000                     

--日期描述与输入的日期不匹配时返回错误
SELECT TO_TIMESTAMP( '2020-01-01,6','YYYY-MM-DD,DDD' ) res FROM DUAL;
[1:22]YAS-00008 type convert error : not a valid day

--不含连接字符的expr
SELECT TO_TIMESTAMP('2020010102','YYYY-MM-DD-HH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000

SELECT TO_TIMESTAMP('2020010102','YYYYMMDDHH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000

--不含连接字符的expr，数字与转换格式长度不相同可能导致错误。本例中26为年份,06为月份，21为日期，02为小时。
SELECT TO_TIMESTAMP('26062102','YYY-MM-DD-HH') res FROM DUAL;
[1:21]YAS-00008 type convert error : not a valid month

--不含连接字符的expr，format中不能为可变长度的转换格式
SELECT TO_TIMESTAMP('2020010102','YYYY-MONTH-DD-HH') res FROM DUAL;
[1:21]YAS-00008 type convert error : literal does not match format string

SELECT TO_TIMESTAMP('20602','DDD-HH') res FROM DUAL;
```

**nls_calendar**

指定数据库使用的日历系统，仅支持为字符串类型，默认值为gregorian。

目前YashanDB仅支持gregorian日历系统。

示例

```sql
SELECT TO_TIMESTAMP('2020010102','YYYYMMDDHH', 'nls_calendar = gregorian') res FROM DUAL;
RES
----------------------------------------------------------------
2020-01-01 02:00:00.000000
```
