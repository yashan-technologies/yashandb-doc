```ebnf
div = DIV "(" expr1 "," expr2 ")".
```

DIV函数执行除法运算，运算规则为：

*   对于小数（FLOAT/DOUBLE/NUMBER），与[算术运算符](../运算符/算术运算符)/算法一致。
*   对于整数，作整除算术运算并返回商数。

在算术运算时，YashanDB通过隐式数据转换，将参与运算的数据类型统一到某个数据类型，并按此数据类型返回运算结果，具体规则请参考[算术运算符](../运算符/算术运算符)里的数据类型描述。

[expr](../通用SQL语法/expr)1、[expr](../通用SQL语法/expr)2的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回不支持该运算。

当expr1或expr2中任一值为NULL时，函数返回NULL。

当expr2的值为0，且expr1的值为非浮点数值时，函数返回Divided by zero错误。

当expr1或expr2为浮点类型特殊值或0时，函数处理规则见下表：

| expr1 | expr2 | DIV(expr1,expr2) |
| --- | --- | --- |
| Nan | 任意数 | Nan |
| 任意数 | Nan | Nan |
| 除Nan/Inf/-Inf外任意数 | Inf/-Inf | 0   |
| Inf/-Inf | 除Nan/Inf/-Inf外任意数 | Inf/-Inf |
| Inf/-Inf | Inf/-Inf | Nan |
| 正浮点数 | 0   | Inf |
| 负浮点数 | 0   | \-Inf |

示例

```sql
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT DIV(numberb,numbera) div1,
DIV(numberd,numbere) div2,
DIV(numberf,numberc) div3,
DIV(numbera,'17') div4,
DIV(numberf,0) div5
FROM numbers_nobit;
                DIV1        DIV2        DIV3        DIV4        DIV5
--------------------- ----------- ----------- ----------- -----------
                  -11    1.0E+018    1.0E-003  -.29411765         Inf
 
SELECT TYPEOF(DIV(numberb,numbera)) type1,
TYPEOF(DIV(numberd,numbere)) type2,
TYPEOF(DIV(numberf,numberc)) type3,
TYPEOF(DIV(numbera,'17')) type4,
TYPEOF(DIV(numberf,0)) type5
FROM numbers_nobit;
TYPE1      TYPE2    TYPE3     TYPE4    TYPE5    
---------- -------- --------- -------- ---------
bigint     float    double    number   double   
 
SELECT DIV(CAST('Nan' AS FLOAT), CAST('Inf' AS DOUBLE)) ndi,
DIV(CAST('-Inf' AS FLOAT), 0) id0,
DIV(12, CAST('-Inf' AS DOUBLE)) ndi,
DIV(12, CAST('Nan' AS FLOAT)) mdn,
DIV(CAST('-Inf' AS FLOAT), CAST('Inf' AS DOUBLE)) idi
FROM DUAL;
        NDI         ID0         NDI         MDN         IDI
----------- ----------- ----------- ----------- -----------
        Nan        -Inf           0         Nan         Nan
```
