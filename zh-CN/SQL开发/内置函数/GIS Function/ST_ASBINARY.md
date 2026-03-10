```ebnf+diagram
st_asbinary::= ST_ASBINARY "(" geometry ["," byteorder ]")"
```
```ebnf+diagram
st_asbinary::= ST_ASBINARY "(" box2d ["," byteorder ]")"
```

ST_ASBINARY函数根据输入的geometry（box2d）和byteorder，返回该geometry（box2d对应geometry）的WKB（Well-Known Binary）表示。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**box2d**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的BOX2D类型的数据。对于该参数，输出规则如下：

*   如果box2d是一个点，则输出对应POINT的WKB。
*   如果box2d是一个线，则输出对应LINESTRING的WKB。
*   如果box2d是一个矩形，则输出对应POLYGON的WKB。
*   输出的geometry的SRID为0。

**byteorder**

byteorder表示输出结果的字节序，其值为VARCHAR类型，遵循如下规则：

*   支持能够隐式转换成VARCHAR的数据类型。
*   "NDR"表示小端序，"XDR"表示大端序，不区分大小写，输入其他字符串则报错。
*   该参数可以省略，省略时默认为编码使用服务器计算机字节序。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
--输出WKB不包含SRID
SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

--大小端序
SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)'), 'NDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)'), 'XDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
00000000013FF00000000000004000000000000000
    
--box2d为点
SELECT ST_AsBinary(BOX2D(1, 1, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
0101000000000000000000F03F0000000000000040

--box2d为线
SELECT ST_AsBinary(BOX2D(1, 3, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
010200000002000000000000000000F03F000000000000004000000000000008400000000000000040

--box2d为矩形    
SELECT ST_AsBinary(BOX2D(1, 3, 2, 3)) res FROM DUAL;

RES
----------------------------------------------------------------
01030000000100000005000000000000000000F03F0000000000000040000000000000F03F00000000000008400000000000000840000000000000084000000000000008400000000000000040000000000000F03F0000000000000040
```
