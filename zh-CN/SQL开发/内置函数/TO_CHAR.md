```ebnf+diagram
to_char::= TO_CHAR "(" expr ["," (format|csid)] ")"
```

TO_CHAR函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值按format格式转换为CHAR/VARCHAR类型字符串数据。

YashanDB支持将所有普通类型的数据转换为CHAR/VARCHAR类型的数据：

根据支持类型可将TO_CHAR函数分为如下三类：

* TO_CHAR（日期时间型）、TO_CHAR（日期时间型，FORMAT）：expr的值为日期时间型时，支持携带格式符；此时函数返回VARCHAR类型字符串数据。
* TO_CHAR（数值型）、TO_CHAR（数值型，FORMAT）：expr的值为数值型时，支持携带格式符；此时函数返回VARCHAR类型字符串数据。
* TO_CHAR（非日期/数值的其他类型）：
  * expr的值为字符型并且携带格式符时，expr尝试往NUMBER类型转换，转换成功则按照TO_CHAR（数值型，FORMAT）执行，转换失败则函数返回转换报错。
  * 不携带格式符时，当expr的值CHAR类型，函数返回CHAR类型字符串数据，否则返回VARCHAR类型字符串数据。
* TO_CHAR（BFILE型）：expr的值为BFILE类型时，支持携带字符集csid信息，函数返回VARCHAR类型字符串数据。

**expr**

通用表达式，需要被转换的数据。

- 当expr的值为NULL时，函数返回NULL。
- expr支持LOB类型隐式转换，但不能为超过65534字节的BFILE、LOB类型数据。
- 当expr的值超过NUMBER类型的表示范围时，数会返回数据溢出错误。
- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

**format**

指定转换的格式。

format支持中文年月日，须用双引号包围中文字符，expr中的中文字符无须用双引号包围。

日期时间型数据的格式支持以下字符的组合：

* 年
  * 年份：'YYYY'、'YYY'、'YY'、'Y'、'RRRR'、'RR'（其中`RRRR`、`RR`仅适用于HEAP表）
  * 由ISO标准定义下的年份： 'IYYY'、'IYY'、'IY'、'I'（仅适用于HEAP表）
  * 一年中的一天（1-366）：'DDD'
  * 一年中的一周（1-53）：'WW'
  * 由ISO标准定义下一年中的一周(1-53)：'IW'（仅适用于HEAP表）
  * 世纪：'CC'
* 月
  * 月份数字：'MM'
  * 月份全称：'MONTH'
  * 月份缩写：'MON'
* 季度
  * 季度（1-4）：'Q'（仅适用于HEAP表）
* 日
  * 日期数字：'DD'
* 周
  * 一个月中的一周（1-5）：'W'
  * 一周中的一天全称（SUNDAY-SATURDAY）：'DAY'
  * 一周中的一天数字（1-7）：'D'
* 时
  * 24小时制小时：'HH24'
  * 12小时制小时：'HH'、'HH12'
  * 12小时制时段：'AM'、'PM'、'A.M.'、'P.M.'
* 分
  * 分钟数：'MI'
* 秒
  * 秒数：'SS'
  * 一天的总秒数：'SSSSS'（仅适用于HEAP表）
* 儒略日（不支持向量化计算）
  * 儒略日计数：`J`
  * 儒略日计数英文全拼：`JSP`
*   连接字符：':', '-', '/', '.', ',', ';', '\\', '_', ' ', '[', ']'
*   去掉前后空格和多余的0：`FM`，例如'FMYY'（仅适用于HEAP表）

> **Note**: 
>
> - 有符号年份'SYYYY'格式符暂未实现，目前和无符号年份'YYYY'格式符完全等价。
> - 有符号世纪'SCC'格式符暂未实现，目前和无符号世纪'CC'格式符完全等价。

数值型数据的格式支持以下字符的组合：

*   小数点：`.`，例如'99.99'
*   美元符号：`$`，例如'$9,999'
*   强迫零显示：`0`，例如'00000'
*   指定位置返回数字：`9`，例如'9999'
*   指定位置返回小数点：`D`，例如'99D99'
*   设首位或末尾为-或+：`S`，例如'S9999'
*   千位分隔符：`,`，例如'9,999'
*   在指定位置返回千位分隔符：`G`，例如'9G9'
*   去掉前后空格和小数后面多余的0：`FM`，例如'FM999'

（以下为列存表专用格式）
*   当整数部分为零时，返回定点数字整数部分的空格：`B`，例如'B99'
*   在指定位置返回货币符号：`C`，例如'C99'
*   以科学计数法的形式返回数字：`EEEE`，例如'9EEEE'
*   在指定位置显示货币符号：`L`，例如'L99''
*   负值末尾填充负号，正值末尾填充空格：`MI`，例如'9MI'
*   负值放在<>中，正值首尾填充空格：`PR`，例如'9PR'
*   返回大写罗马数字形式：`RN`，例如'RN'
*   返回小写罗马数字形式：`rn`，例如'rn'
*   以最少的字符数返回十进制数字字符串：`TM`，例如'TM'
*   在指定位置返回欧元等货币符号：`U`，例如'U99'
*   返回10<sup>n</sup>值，其中n是V后面的数字：`V`，例如'99V99'
*   返回指定数字的十六进制值：`X`，例如'XXX'

当expr的值为布尔型的数据时，不需要指定format格式，指定会报错。

**csid**

当expr为BFILE类型时，指定BFILE数据的字符集ID。如果BFILE数据的字符集与数据库当前字符集一致，则可以将csid的值指定为0，或完全省略csid。


csid可配置范围如下：

|  字符集| csid值|
|:---------|:------|
| ASCII    | 1     |
| GBK      | 852   |
| UTF-8    | 873   |
| ISO88591 | 31    |
| UTF-16   | 2000  |
| GB18030  | 854   |


示例

```sql
SELECT TO_CHAR('Shenzhen') res1,TO_CHAR(True) res2 FROM DUAL;
RES1      RES2  
--------- ----- 
Shenzhen  true 
```

当expr的值为日期时间型的数据时，不指定format表示系统对该日期类型指定的默认格式转换。

示例

```sql
SELECT TO_CHAR(SYSTIMESTAMP+1) res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-01-10 22:09:27   
 
SELECT TO_CHAR(SYSDATE,'YYYYMMDD HH24:MI:SS') res FROM DUAL;
RES                                            
----------------------------------------------------------------
20220109 22:09:27

SELECT TO_CHAR(SYSDATE,'YYYY"年"MM"月"dd"日"') RES FROM DUAL;
RES
--------------------------------
2023年11月09日
```

当expr的值为数值型的数据时，不指定format表示按该数据的字面值转换为字符串。

示例（HEAP表）
```sql
SELECT TO_CHAR(numbera,'00000') n1,
TO_CHAR(numberb,'99.99') n2,
TO_CHAR(numberc,'$99999999') n3,
TO_CHAR(numberd,'9999999999999999999') n4,
TO_CHAR(numbere,'99D99') n5,
TO_CHAR(numberf) n6,
TO_CHAR(numberg,'9999999999S') n7,
TO_CHAR(numberh) n8
FROM numbers;
N1      N2      N3         N4                    N5        N6                         N7        N8 
------- ------- ---------- --------------------- --------- -------------------------- --------- ----
-00005   55.00    $5555     5555555555555555555    5.55    -5.5555555500000002E+000      555+   1
```

示例（TAC、LSC表）
```sql
SELECT TO_CHAR(numbera,'00000') n1,
TO_CHAR(numberb,'99.99') n2,
TO_CHAR(numberc,'$99999999') n3,
TO_CHAR(numberd,'9999999999999999999') n4,
TO_CHAR(numbere,'99D99') n5,
TO_CHAR(numberf) n6,
TO_CHAR(numberg,'9999999999S') n7
FROM numbers_nobit;
N1        N2        N3            N4                    N5        N6                                            N7
--------- --------- ------------- --------------------- --------- --------------------------------------------- -------------
-00005     55.00         $5555     5555555555555555555    5.55    5.5555555555499998E+000                              555+
```

当expr的值为字符型的数据时，不指定format表示按该数据的字面值转换为字符串。

示例（HEAP表）
```sql
SELECT TO_CHAR(CAST(numbera AS VARCHAR(30)),'00000') n1,
       TO_CHAR(CAST(numberb AS VARCHAR(30)),'99.99') n2,
       TO_CHAR(CAST(numberc AS VARCHAR(30)),'$99999999') n3,
       TO_CHAR(CAST(numberd AS VARCHAR(30)),'9999999999999999999') n4,
       TO_CHAR(CAST(numbere AS VARCHAR(30)),'99D99') n5,
       TO_CHAR(CAST(numberf AS VARCHAR(30))) n6,
       TO_CHAR(CAST(numberg AS VARCHAR(30)),'9999999999S') n7,
       TO_CHAR(CAST(numberh AS VARCHAR(30))) n8
FROM numbers;

N1        N2        N3            N4                    N5        N6                                N7            N8
--------- --------- ------------- --------------------- --------- --------------------------------- ------------- ---------------------------------
-00005     55.00         $5555     5555555555555555555    5.55    -5.5555555500000002E+000                 555+   1
```

示例（TAC、LSC表）
```sql
SELECT TO_CHAR(CAST(numbera AS VARCHAR(30)),'00000') n1,
       TO_CHAR(CAST(numberb AS VARCHAR(30)),'99.99') n2,
       TO_CHAR(CAST(numberc AS VARCHAR(30)),'$99999999') n3,
       TO_CHAR(CAST(numberd AS VARCHAR(30)),'9999999999999999999') n4,
       TO_CHAR(CAST(numbere AS VARCHAR(30)),'99D99') n5,
       TO_CHAR(CAST(numberf AS VARCHAR(30))) n6,
       TO_CHAR(CAST(numberg AS VARCHAR(30)),'9999999999S') n7
FROM numbers_nobit;

N1        N2        N3            N4                    N5        N6                                N7
--------- --------- ------------- --------------------- --------- --------------------------------- -------------
-00005     55.00         $5555     5555555555555555555    5.55    5.5555555555499998E+000                  555+
```

当expr的值为BFILE类型的数据时，支持携带字符集csid信息，表示按相应字符集解析BFILE文件后转换为字符串。

示例（HEAP表）

```sql
CREATE OR REPLACE DIRECTORY TMP_DIR AS '/data/tmp';
!echo "test file ὠ3ὠ4" > '/data/tmp/char.txt';
 SELECT TO_CHAR(BFILENAME('TMP_DIR', 'char.txt')) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4

-- 指定utf-8字符集
SELECT TO_CHAR(BFILENAME('TMP_DIR', 'char.txt'), 873) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4
```
