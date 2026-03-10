```ebnf+diagram
st_difference::= ST_DIFFERENCE "(" geometry1 "," geometry2 [ "," girdsize]")"
```

ST_DIFFERENCE函数返回包含geometry1但不包含geometry2几何图形，返回值为ST_GEOMETRY类型数据。

函数会将geometry对象投射到网格线上进行计算并返回结果。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

**gridsize**

gridsize的数据类型为DOUBLE，表示函数计算时使用的网格线大小，省略则默认为-1。

本函数遵守如下规则：

* 严格按照参数输入顺序决定输出内容，返回值一定与geometry1相关。
* 当geometry1完全包含在geometry2中时，函数返回geometry1类型的EMPTY。
* 当geometry1和geometry2均为EMPTY或任意一个为EMPTY时，函数均返回geometry1。
* 当输入的参数存在NULL时，函数返回NULL。
* 当输入的参数中包含Nan时，函数返回错误。
* 支持输入3D坐标，但函数会忽略Z坐标进行计算。

示例（HEAP表）

```sql
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
MULTILINESTRING ((0 20, 0 30), (0 60, 0 80)) 

--geometry2完全包含geometry1时返回geometry1类型的EMPTY
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 30)'),ST_GEOMFROMTEXT('POLYGON((0 0, 0 60,60 60, 60 0,0 0))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY  

--geometry1和geometry2均为空时返回geometry1
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING EMPTY'),ST_GEOMFROMTEXT('POLYGON EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY 

--geometry1和geometry2其中任意一个为空时均返回geometry1
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING EMPTY'),ST_GEOMFROMTEXT('POLYGON((0 0, 0 60,60 60, 60 0,0 0))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY    

SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 30)'),ST_GEOMFROMTEXT('POLYGON EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING (0 20, 0 30)     

--gridsize为NULL时返回NULL
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)'),NULL), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 


--参数中存在NULL时函数返回NULL
SELECT ST_ASTEXT(ST_DIFFERENCE(NULL,ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
   
   
--SRID不同时返回错误
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)',10),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)',6)), 0) res FROM DUAL;

YAS-07202 plugin execution error, Operation on mixed SRID geometries: 10 != 6

--参数中含有Nan时返回错误
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 Nan, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

YAS-07202 plugin execution error, LINESTRING has invalid coordinate
```
