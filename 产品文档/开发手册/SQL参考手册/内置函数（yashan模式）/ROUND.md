```ebnf+diagram
round::= ROUND "(" ((expr ["," fmt])| (expr ["," round_number])) ")"
```

ROUND函数对[expr](../通用SQL语法/expr)的值按照指定格式四舍五入一个日期值，返回一个DATE类型的日期；或按照round_number指定的位数进行四舍五入，返回类型为：

| expr类型 | 返回值 | 备注  |
| --- | --- | --- |
| TINYINT | SMALLINT |     |
| SMALLINT | INT |     |
| INT | BIGINT |     |
| BIGINT | NUMBER |     |
| NUMBER | NUMBER |     |
| FLOAT | FLOAT/NUMBER | 未指定round_number时输出FLOAT，否则输出NUMBER。 |
| DOUBLE | DOUBLE/NUMBER | 未指定round_number时输出DOUBLE，否则输出NUMBER。 |

当用于四舍五入日期值时，expr的值必须为DATE、TIMESTAMP、时区类型。

当用于四舍五入数值时，expr的值必须为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。当expr的值为0时，函数返回0。

对于其他类型，函数返回类型转换错误。

当expr的值为NULL时，函数返回NULL。

**fmt**

指定日期值的截断格式，规则如下：

| 类型  | 有效格式                                 | 返回值 | 备注  |
| --- |--------------------------------------| --- | --- |
| Century | CC, SCC                              | 大于'XX50'圆整到下一世纪第一天，否则返回当世纪第一天 |  |
| Year | SYYYY, YYYY, YEAR, SYEAR, YYY, YY, Y | 从七月开始圆整到下一年第一天，否则本年第一天 |     |
| ISO Year | IYYY, IY, I                          | 将每年的第一周作为基准，返回该周的第一天，从七月开始圆整到下一年第一天，否则本年第一天 | ISO当年第一天可能是公历前一年12月末 |
| Quarter | Q                                    | 在每个季度中的第二月的第16天后圆整到下一季度第一天 |     |
| Month | MONTH, MON, MM, RM                   | 在每个月的第十六天后圆整到下个月第一天 |     |
| Week | WW                                   | 每年的1月1日作为当年的第一周的第一天，从每周的第五天圆整到下周的第一天，否则返回当周第一天 |  |
| IW  | IW                                   | 星期一为每周的第一天，从每周的第五天开始圆整到下周的第一天，否则返回当周第一天 |     |
| W   | W                                    | 每月1日作为本月第一周的第一天，从每周的第五天开始圆整到下周的第一天，否则返回当周第一天 |     |
| Day | DDD, DD, J                           | 返回天，从一天的中午`12:00`开始圆整到下一天0分0秒，否则返回该天的0分0秒 |     |
| Start day of the week | DAY, DY, D                           | 星期日作为每周第一天，从每周的第五天开始圆整到下周的第一天，否则返回当周第一天 |     |
| Hour | HH, HH12, HH24                       | 返回小时，从每小时的第三十分钟开始圆整到下一小时0分0秒，否则返回该小时的0分0秒 |     |
| Minute | MI                                   | 返回分钟，从每分钟的30秒开始圆整到下一分钟0秒，否则返回该分钟0秒 |     |
| null | null                                 | null |     |

当不指定fmt时，默认为'DD'。

对于类型IW、W、 WW、Start day of the week，当输入值的小时数大于等于12时，进位可跨天、跨月、跨年。 

**round_number**

指定截取的小数位数，须为数值型数据，或可转换为NUMBER类型的其他类型数据。

round_number为与expr相同的通用表达式，当round_number的值为NULL时，函数返回NULL。  

当round_number的值为正数，且该值大于expr的小数位数时，函数返回expr的原值。

当round_number的值为负数时，表示向小数点左边截取，例如"ROUND(123.456,-2)"，返回的是123四舍五入至小数点左侧第2位的数字，即100。此时当round_number的绝对值大于小数点左边的整数位数时，函数返回0，例如"ROUND(123.456,-4)=0"。

  

示例

```sql
SELECT ROUND(SYSDATE-10000) res FROM DUAL;
RES             
-------------------------------- 
1995-02-05 

SELECT ROUND(SYSDATE-10000) res FROM DUAL;
RES             
-------------------------------- 
1995-02-05 

SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT ROUND(numbere,numbera) round1,
ROUND(numbere,numberb) round2,
ROUND(numberf,15660) round3,
ROUND(numbere,'17') round4,
ROUND(numberf,-1) round5
FROM numbers_nobit;
     ROUND1      ROUND2      ROUND3      ROUND4      ROUND5
----------- ----------- ----------- ----------- -----------
          0  5.55499983  5.55555556  5.55499983          10
 
 
SELECT TYPEOF(ROUND(numbere,numbera)) type1,
TYPEOF(ROUND(numbere,numberb)) type2,
TYPEOF(ROUND(numberf,15660)) type3,
TYPEOF(ROUND(numbere,'17')) type4,
TYPEOF(ROUND(numberf,-1)) type5
FROM numbers_nobit;
TYPE1    TYPE2    TYPE3     TYPE4     TYPE5  
-------- -------- --------- --------- --------
number   number   number    number    number
```

