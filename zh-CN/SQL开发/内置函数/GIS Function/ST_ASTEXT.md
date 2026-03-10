```ebnf+diagram
st_astext::= ST_ASTEXT "(" geometry [ "," maxdecimaldigits ] ")"
```
```ebnf+diagram
st_astext::= ST_ASTEXT "(" box2d [ "," maxdecimaldigits ] ")"
```

ST_ASTEXT函数根据输入的geometry（box2d）和maxdecimaldigits，返回该geometry（box2d对应geometry）的WKT（Well-Known Text）表示。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**box2d**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的BOX2D类型的数据。对于该参数，输出规则如下：

*   如果box2d是一个点，则输出对应POINT的WKT。 
*   如果box2d是一个线，则输出对应LINESTRING的WKT。
*   如果box2d是一个矩形，则输出对应POLYGON的WKT。
*   输出的geometry的SRID为0。

**maxdecimaldigits**

maxdecimaldigits表示输出结果中小数的位数，如果不够则补零，其值为INT类型，遵循如下规则：

*   支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。
*   该参数可以省略，省略时默认值是15。
*   如果输入的是负数，则会按照0输出。
*   如果输入的值超过了16，则会给按照16进行处理，但第16位小数不一定有效。
*   结果不会以科学计数法的形式输出。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
--小数位数
SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 1) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1.0 2.0)                                                 

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 100) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1.0000000000000000 2.0000000000000000)                   

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), -10) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1 2)                                                     
                                                   
--维度混合
SELECT ST_AsText(ST_GeomFromText('MULTIPOINT(1 2 3, 1 1)', 4326), 0) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
MULTIPOINT Z (1 2 3, 1 1 0)                                     

--支持inf和nan的输入
SELECT ST_AsText(St_GeomFromText('MULTIPOINT(inf inf,nan nan)')) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
MULTIPOINT (inf inf, EMPTY)
    
--box2d为点
SELECT ST_AsText(BOX2D(1, 1, 2, 2), 0) res FROM DUAL;

RES
----------------------------------------------------------------
POINT (1 2)
    
--box2d为线   
SELECT ST_AsText(BOX2D(1, 3, 2, 2), 0) res FROM DUAL;

RES
----------------------------------------------------------------
LINESTRING (1 2, 3 2)

--box2d为矩形    
SELECT ST_AsText(BOX2D(1, 3, 2, 3), 0) res FROM DUAL;

RES
----------------------------------------------------------------
POLYGON ((1 2, 1 3, 3 3, 3 2, 1 2))
```
