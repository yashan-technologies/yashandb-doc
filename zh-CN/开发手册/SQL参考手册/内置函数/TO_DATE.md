```ebnf
to_date = TO_DATE "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format [","nls_calendar]] ")".
```

TO\_DATE函数将[expr](../通用SQL语法/expr)的值转换为DATE类型数据，返回值格式为DATE类型数据的默认格式。

**expr**

expr的值须为字符型或可以转化为字符型的类型（LOB类型支持隐式转换），且其内容须符合format格式，否则返回格式转换错误。

当expr的值为NULL时，函数返回NULL。

对于列存表中的LOB类型字段，若某行数据为行外存储，则无法使用本函数。

**DEFAULT replace\_expr ON CONVERSION ERROR**

表示当对expr转换失败时，使用replace\_expr值来进行转换，replace\_expr为一个字符串字面量，其内容须符合format格式。

示例

```sql
SELECT TO_DATE('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES            
--------------------------------
2022-02-13 00:00:00
```

**format**

format用于指定expr的时间格式，其值须为字符型，可省略，省略则默认expr格式同DATE类型的默认格式。

format支持中文年月日，使用时须用双引号包围中文字符，且format中不能指定为可变长度的转换格式，例如MON、MONTH、DAY；expr中的中文字符无须用双引号包围。

如果format中未指定年，则会获取当前年份然后填充；如果不存在月，则会获取当前月份然后填充；如果不存在日，则会用1来填充；未指定时间用0来填充。

YashanDB支持不含连接字符的expr与format进行匹配，该匹配须遵循如下规则：

- expr中数字会按照format指定的格式进行转换，建议输入相同长度的数字和转换格式，否则可能导致结果错误。
- 列存表中，format必须为包含连接字符的时间格式。
- format中不能指定为可变长度的转换格式，例如MONTH、DAY。

指定转换的格式，包括如下字符组合：

* 年
    * 年份数字：`YYYY`、`YYY`、`YY`、`Y`、`RRRR`、`RR`（其中`RRRR`、`RR`仅适用于HEAP表）
* 月
    * 月份数字：`MM`
    * 月份全称：`MONTH`
    * 月份缩写：`MON`
* 日
    * 日期数字：`DD`
* 日期描述
    * 一周中的一天全称（SUNDAY-SATURDAY）：`DAY`
    * 一周中的一天数字（1-7）：`D`
    * 一年中的一天（1-366）：`DDD`
* 时
    * 24小时制小时：`HH24`
    * 12小时制小时：`HH`、`HH12`
* 分
    * 分钟数：`MI`
* 秒
    * 秒数：`SS`
    * 一天的总秒数：`SSSSS`（仅适用于HEAP表）
* 儒略日
  * 儒略日计数：`J`（仅适用于HEAP表）
*   连接字符：`:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_`、 ` ` 、 `[`、 `]`
    * `:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_` 、 ` ` 、 `[`、 `]`：连接符之间可以相互匹配
    * ` `：空格可不参与匹配

> **Note**: 
>
> 日期描述类格式须与年月日格式同时指定，且expr中日期描述须与年月日匹配，否则返回错误。
> 有符号年份'SYYYY'格式符暂未实现，目前和无符号年份'YYYY'格式符完全等价。
> 当儒略日计数范围在[1,1721057]之内，对应实际年份为负数。

其中对于年格式的指定存在如下转换规则：

| expr\\format | Y   | YY  | YYY | YYYY |
| --- | --- | --- | --- | --- |
| 1   | 2021-06-01 | 2001-06-01 | 2001-06-01 | 0001-06-01 |
| 12  | 报错  | 2012-06-01 | 2012-06-01 | 0012-06-01 |
| 123 | 报错  | 0123-06-01 | 2123-06-01 | 0123-06-01 |
| 1234 | 报错  | 1234-06-01 | 报错  | 1234-06-01 |

示例

```sql
-- expr包含连接字符
SELECT TO_DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YY, HH:MI A.M.' ) res FROM DUAL;
RES                               
--------------------------------
1989-01-15 11:00:00    
 
SELECT TO_DATE( '1989/2/23', 'YYYY/MM/DD HH24:MI:SS' ) res FROM DUAL;
RES                               
--------------------------------
1989-02-23 00:00:00

SELECT TO_DATE( '2020-01-01,1','YYYY-MM-DD,DDD' ) res FROM DUAL;
RES
-------------------------------- 
2020-01-01 00:00:00                    

-- 日期描述与输入的日期不匹配时返回错误
SELECT TO_DATE( '2020-01-01,6','YYYY-MM-DD,DDD' ) res FROM DUAL;
[1:17]YAS-00008 type convert error : not a valid day

SELECT TO_DATE('0120111-','YYYMMDD-') res FROM DUAL;
RES                              
-------------------------------- 
2012-01-11 00:00:00                      

-- 不兼容匹配默认DATE格式'YYYY-MM-DD'时，返回错误
SELECT TO_DATE( '1989//2/23' ) res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string

-- expr中不含连接字符
-- format中包含连接字符
SELECT TO_DATE('20120111','YYYY-MM-DD') res FROM DUAL;
RES                              
-------------------------------- 
2012-01-11 00:00:00                      

-- format中不能为可变长度的转换格式
SELECT TO_DATE('2012JANUARY11','YYYY-MONTH-DD') res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string

-- 数字会按照format的转换格式进行转换，本例中12为年份，01为月份，30为日期。由于转换格式为YYY-MM-DD，将120识别为年份，13识别为月份导致错误
SELECT TO_DATE('120130','YYY-MM-DD') res FROM DUAL;
[1:16]YAS-00008 type convert error : not a valid month

-- expr和format均不含连接字符
SELECT TO_DATE('20120111','YYYYMMDD') res FROM DUAL;
RES
-------------------------------- 
2012-01-11 00:00:00 

SELECT TO_DATE('2010131','YYYMMDD') res FROM DUAL;
RES
--------------------------------
2201-01-31 00:00:00 

SELECT TO_DATE('2016','YYYMM') res FROM DUAL;
RES                              
-------------------------------- 
2201-06-01 00:00:00 
    
SELECT TO_DATE('2012年01月01日','YYYY"年"MM"月"dd"日"') RES FROM DUAL;

RES
--------------------------------
2012-01-01 00:00:00

SELECT TO_DATE('1721424','J') res FROM DUAL;
RES
-------------------------------- 
0001-01-01 00:00:00
```

**nls_calendar**

指定数据库使用的日历系统，仅支持为字符串类型，默认值为gregorian，否则返回类型错误，当前YashanDB仅支持日历系统为gregorian的情况，其他情况将会报错。

示例

```sql
SELECT TO_DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YYYY, HH:MI A.M.', 'nls_calendar = gregorian' ) res FROM DUAL;
RES                                
-------------------------------- 
1989-01-15 11:00:00 
```
