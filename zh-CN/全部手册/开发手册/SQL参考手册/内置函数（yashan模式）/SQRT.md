```ebnf+diagram
sqrt::= SQRT "(" expr ")"
```

SQRT函数计算[expr](../通用SQL语法/expr)表示的数据的平方根。其返回类型为：

*   当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER、CHAR、VARCHAR、NCHAR或NVARCHAR类型时，返回NUMBER。
*   当expr的值为FLOAT类型时，返回FLOAT。
*   当expr的值为DOUBLE类型时，返回DOUBLE。
*   当expr的值为NULL时，返回NULL。

其中expr的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为负数时，函数返回Out or range错误，为负数且数据类型为float或double时，函数返回无效数字Nan。

示例

```sql
-- numbers_nobit表中包含如下字段和数据
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT SQRT(numberb) sqrt1,
SQRT(numberc) sqrt2,
SQRT(numberd) sqrt3,
SQRT(numbere) sqrt4,
SQRT(numberg) sqrt5
FROM numbers_nobit;
      SQRT1       SQRT2       SQRT3       SQRT4       SQRT5
----------- ----------- ----------- ----------- -----------
 7.41619849  74.5318724  2357022604  2.357E+000   23.558438
 
 
SELECT TYPEOF(SQRT(numberb)) type1,
TYPEOF(SQRT(numberc)) type2,
TYPEOF(SQRT(numberd)) type3,
TYPEOF(SQRT(numbere)) type4,
TYPEOF(SQRT(numberg)) type5
FROM numbers_nobit;
TYPE1       TYPE2       TYPE3      TYPE4     TYPE5       
----------- ----------- ---------- --------- ---------   
number      number      number     float     number
```
