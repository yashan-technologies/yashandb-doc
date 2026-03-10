```ebnf+diagram
mod::= MOD "(" expr1 "," expr2 ")"
```

MOD为取模函数，与[算术运算符](../运算符/算术运算符)算法一致。

在算术运算时，YashanDB通过隐式数据转换，将参与运算的数据类型统一到某个数据类型，并按此数据类型返回运算结果，具体规则请参考[算术运算符](../运算符/算术运算符)里的数据类型描述。

[expr](../通用SQL语法/expr)1、[expr](../通用SQL语法/expr)2的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr1或expr2中任一值为NULL时，函数返回NULL。

示例

```sql
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
 NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF   NUMBERG
-------- ------- -------- --------------------- ----------- ----------- ---------
      -5      55     5555   5555555555555555555  5.555E+000  5.556E+000       555
 
 
SELECT MOD(numberb,numbera) mod1,
MOD(numberd,numbere) mod2,
MOD(numberf,numberc) mod3,
MOD(numbera,'17') mod4,
MOD(numberf,0) mod5
FROM numbers_nobit;
                 MOD1        MOD2        MOD3        MOD4        MOD5
--------------------- ----------- ----------- ----------- -----------
                    0  3.794E+000  5.556E+000          -5  5.556E+000
 
SELECT TYPEOF(MOD(numberb,numbera)) type1,
TYPEOF(MOD(numberd,numbere)) type2,
TYPEOF(MOD(numberf,numberc)) type3,
TYPEOF(MOD(numbera,'17')) type4,
TYPEOF(MOD(numberf,0)) type5
FROM numbers_nobit;
TYPE1     TYPE2     TYPE3     TYPE4     TYPE5       
--------- --------- --------- --------- ---------   
bigint    float     double    number    double
```
