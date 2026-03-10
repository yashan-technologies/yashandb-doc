```ebnf+diagram
date_sub::= DATE_SUB "(" expr "," INTERVAL interval_value interval_unit ")"
```

DATE_SUB函数用于执行日期运算，通过[expr](../通用SQL语法/expr)的值减去给定的区间值得到时间推进或后退过的结果。

**expr**

通用表达式，expr的值须为DATE、TIME、TIMESTAMP类型或可以转换为DATE、TIMESTAMP类型的字符类型。

- 当expr为字面量时，只能为DATE和TIMESTAMP关键字的输入字符串，不能为TIME关键字的输入字符串。例如，DATE '2012-10-12'、TIMESTAMP '2012-10-12 10:20:24.000006'为函数可接受的expr值，而当expr为TIME '10:20:24'时函数则返回错误。

- 当expr为NULL时，函数返回NULL。

**interval_value**

指定时间前进或后退的区间值，且必须为如下形式：

- 0或正整数字面量， 例如0、1、2等。
- 包含0或正整数内容的字符串字面量， 例如'0'、'1'、'2'等。
- 包含负整数内容的字符串字面量， 例如'-1'、'-2'等。
- 包含INTERVAL内容的字符串字面量， 例如'10-8'、'-8 8:10:24'等。

**interval_unit**

指定区间值的单位，该值不可为NULL，且必须为如下形式：

- MONTH、YEAR、YEAR TO MONTH关键字（不区分大小写）：此时interval_value被转换为INTERVAL YEAR TO MONTH类型。
- SECOND、MINUTE、HOUR、DAY、MINUTE TO SECOND、HOUR TO SECOND、HOUR TO MINUTE、DAY TO SECOND、DAY TO MINUTE、DAY TO HOUR关键字（不区分大小写）：此时interval_value被转换为INTERVAL DAY TO SECOND类型。

interval_value与interval_unit必须正确匹配，例如分别为2/YEAR，3/HOUR，'10-8'/YEAR TO MONTH、'-8 8:10:24'/DAY TO SECOND，否则函数返回类型转换错误。

**日期运算规则**

- 当interval_value为INTERVAL YEAR TO MONTH类型时，运算规则为：

  - 先进行month的增减，再判断day是否符合month的增减后的day数。

  - 如果增减后的month的天数小于增减前的month的天数，那么增减后的day数等于增减后的month的最后一天。

- 当expr和interval_value的数据类型不相同时，函数先执行类型转换，若两个数据类型之间无法按照一定的规则进行转换，则返回类型转换错误。类型转换规则如下：

  - expr为DATE/TIME/TIMESTAMP类型时，无需转换，函数直接返回expr的数据类型。

  - expr为CHAR/VARCHAR类型时，函数将其转换为DATE类型，转换成功则返回转换后的数据类型，否则返回类型转换错误。
  - expr为TIME，interval_value为INTERVAL DAY TO SECOND类型时，超过范围将翻转，即超过24小时求余数，例如计算后的小时为25时，将其翻转为1。
  - expr为TIME，interval_value为INTERVAL YEAR TO MONTH类型时，函数返回类型不支持错误。

示例

```sql
SELECT DATE_SUB('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2013-10-12 00:00:00.000000  

-- interval_value为INTERVAL YEAR TO MONTH类型时
SELECT DATE_SUB('2012-10-31',INTERVAL 1 MONTH) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2012-9-30 00:00:00.000000  

-- interval_value为INTERVAL DAY TO SECOND类型时
SELECT DATE_SUB(CAST('10:20:24' AS TIME),INTERVAL '8:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
02:10:00.000000 

-- 小时翻转
SELECT DATE_SUB(CAST('10:20:24' AS TIME),INTERVAL '14:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
20:10:00.000000 
```
