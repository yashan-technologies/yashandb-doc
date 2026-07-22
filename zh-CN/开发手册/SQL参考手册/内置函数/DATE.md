```ebnf
date = DATE "("expr ["," format] ")" .
```

DATE函数有1或2个参数。当只有一个[expr](../通用SQL语法/expr)参数时，DATE函数将expr的值按照配置参数DATE_FORMAT指定格式进行DATE类型转换；当有两个参数时，DATE函数将expr的值按照第二个参数format进行格式转换。

**expr**

表达式expr的数据类型须为DATE/TIMESTAMP/TIME类型，内容不论是否符合date_format或format格式，都返回正确结果。

当expr的值为NULL时，函数返回NULL。

**format**

format支持中文年月日，须用双引号包围中文字符，expr中的中文字符无须用双引号包围。

指定转换的格式，包括如下字符组合：

* 年
    * 年份数字：`YYYY`、`Y`、`YY`、`YYY`
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
*   连接字符：`:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_`、 ` `
    * `:`、`-`、 `/`、 `.`、 `,`、 `;`、 `\`、 `_`：八种连接符相互一对一兼容匹配
    * `  `：忽略所有空格，空格不参与匹配

不指定format时，系统按DATE类型指定的默认格式进行转换，如果expr内容不符合默认格式，返回格式匹配错误。

当format的值为NULL时，函数返回NULL。

示例

```sql
SELECT DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YY, HH:MI A.M.' ) res FROM DUAL;
RES                               
--------------------------------
1989-01-15 11:00:00    
 
SELECT DATE( '1989/2/23', 'YYYY/MM/DD HH24:MI:SS' ) res FROM DUAL;
RES                             
--------------------------------
1989-02-23 00:00:00

SELECT DATE( '1989/2/23' ) res FROM DUAL;
RES
-------------------------------- 
1989-02-23                      

    
SQL> SELECT DATE( '1989/2/23', '') res FROM DUAL;
RES
--------------------------------

SQL> SELECT DATE( '1989/2/23', null) res FROM DUAL;
RES
--------------------------------

--不兼容匹配默认DATE格式'YYYY-MM-DD'时，返回错误
SELECT DATE( '1989//2/23' ) res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string
```
