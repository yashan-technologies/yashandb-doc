```ebnf+diagram
st_ashexewkb::= ST_ASHEXEWKB "(" geometry "," byteorder ")"
```

```ebnf+diagram
st_ashexewkb::= ST_ASHEXEWKB "(" box2d "," byteorder ")"
```

ST_ASHEXEWKB函数根据给输入的geometry（box2d）和byteorder，返回该geometry（box2d对应geometry）的hexewkb（Extended Well-Known Binary对应的十六进制形式）表示。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**box2d**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的BOX2D类型的数据。对于该参数，输出规则如下：

*   如果box2d是一个点，则输出对应POINT的HEXEWKB。
*   如果box2d是一个线，则输出对应LINESTRING的HEXEWKB。
*   如果box2d是一个矩形，则输出对应POLYGON的HEXEWKB。
*   输出的geometry的SRID为0。

**byteorder**

byteorder的数据类型为VARCHAR，表示输出结果的字节序。

*   支持能够隐式转换成VARCHAR的数据类型。
*   "NDR"表示小端序，"XDR"表示大端序，不区分大小写，输入其他字符串则报错。
*   该参数可以省略，省略时默认使用服务器计算机字节序。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
--输出wkb包含srid
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
0101000020E6100000000000000000F03F0000000000000040              

--大小端序
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), 'NDR') res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), 'XDR') res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
00000000013FF00000000000004000000000000000      

--参数包含NULL
SELECT ST_AsHexEwkb(NULL, 'XDR') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), NULL) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
```
