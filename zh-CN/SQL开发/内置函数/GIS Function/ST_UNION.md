```ebnf+diagram
st_union::= ST_UNION "(" geometry1 "," geometry2 [ ","girdsize]")"
```

ST_UNION函数返回两个geometry对象的并集，返回值为ST_GEOMETRY类型数据。

函数会将geometry对象投射到网格线上进行计算并返回结果。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

**gridsize**

gridsize的数据类型为DOUBLE，表示函数计算时使用的网格线大小，省略则默认为-1。

本函数遵守如下规则：

* 当geometry1和geometry2均为EMPTY时，函数返回geometry1。
* 当geometry1和geometry2其中一个为EMPTY时，函数返回该非空对象。
* 当输入的参数存在NULL时，函数返回NULL。
* 当输入的参数中包含Nan时，函数返回错误。
* 支持输入3D坐标，但函数会忽略Z坐标进行计算。

示例（HEAP表）

```sql
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0.000000000000000 5.000000000000000, 0.375000000000000 5.000000000000000, 0.000000000000000 8.000000000000000, 8.000000000000000 8.000000000000000, 8.000000000000000 0.000000000000000, 5.000000000000000 0.000000000000000, 1.000000000000000 0.000000000000000, 0.000000000000000 0.000000000000000, 0.000000000000000 5.000000000000000))

--geometry1和geometry2均为EMPTY时返回geometry1
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON EMPTY'),ST_GEOMFROMTEXT('LINESTRING EMPTY'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY  

--geometry1和geometry2其中一个为EMPTY时返回非空对象
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('LINESTRING EMPTY'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0.000000000000000 0.000000000000000, 0.000000000000000 5.000000000000000, 5.000000000000000 5.000000000000000, 5.000000000000000 0.000000000000000, 0.000000000000000 0.000000000000000))

--参数存在NULL时返回NULL
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),NULL)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 

SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'),NULL)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
 
--SRID不同时返回错误
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))',6),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))',1))) res FROM DUAL;

YAS-07202 plugin execution error, Operation on mixed SRID geometries: 6 != 1

--参数中含有Nan时返回错误
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 Nan,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'))) res FROM DUAL;

YAS-07202 plugin execution error, POLYGON has invalid coordinate
```
