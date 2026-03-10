```ebnf+diagram
st_clipbybox2d::= ST_CLIPBYBOX2D "(" geometry1 "," geometry2 ")"
```

ST_CLIPBYBOX2D函数返回geometry1中由geometry2计算出的矩形裁剪框裁剪后的几何图形，返回值为ST_GEOMETRY类型数据。

> **Note**: 
>
> 由geometry2计算出的矩形裁剪框坐标为（ Xmin, Ymin, Xmax, Ymax ），其中Xmin为geometry2中X坐标最小值，Ymin为geometry2中Y坐标最小值，Xmax为geometry2中X坐标最大值，Ymax为geometry2中Y坐标最大值。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

本函数遵守如下规则：

* 当被裁剪图形为EMPTY时，函数返回被裁剪图形对应类型的EMPTY。
* 当被裁剪图形为POINT类型或LINESTRING类型时，函数返回GEOMETRYCOLLECTION EMPTY。
* 当被裁剪图形被包含在裁剪框内时，函数返回被裁剪图形。
* 当被裁剪图形与裁剪框完全不相交时，函数返回被裁剪图形类型的EMPTY。
* 当裁剪框为EMPTY、Nan、POINT类型、垂直线和水平线时，函数返回NULL。
* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。

示例（HEAP表）

```sql
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0 2, 2 2, 2 0, 0 2))  

--被裁剪图形为EMPTY时返回对应类型EMPTY
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON EMPTY'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;
          
RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY            

--被裁剪图形为POINT类型时返回GEOMETRYCOLLECTION EMPTY
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POINT(1 2)'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY  

--被裁剪图形被包含在裁剪框内时返回被裁剪图形
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((0 0,0 1, 1 1, 1 0, 0 0))'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0 0, 0 1, 1 1, 1 0, 0 0)) 

--被裁剪图形与裁剪框完全不相交时返回被裁剪图形类型的EMPTY
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((0 0,0 1, 1 1, 1 0, 0 0))'), ST_GEOMFROMTEXT('MULTIPOINT(6 6, 6 7, 7 7, 7 6)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY 

--裁剪框为EMPTY时返回NULL
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), ST_GEOMFROMTEXT('MULTIPOINT EMPTY')), 0) res FROM DUAL;         
          
RES                                                              
---------------------------------------------------------------- 
                                                                
--参数存在NULL时返回NULL
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), NULL), 0) res FROM DUAL;  

RES                                                              
---------------------------------------------------------------- 
                                                                
```
