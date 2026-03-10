```ebnf+diagram
st_centroid::= ST_Centroid"(" geometry "," use_spheroid ")"
```

ST_CENTROID函数用于计算指定几何体的几何质心的点。

本函数遵守如下规则：

* 函数返回多边形质心，不一定位于多边形内部。
* 函数通过指定geometry SRID为4326表示经纬度坐标。
* 函数经纬度坐标计算方式目前只支持椭球体方式。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**use_spheroid**

指定是否使用椭球体方式计算，默认为true。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_Centroid(st_geomfromtext('MULTIPOINT((-15 -15), (5 5))')), 0) FROM dual;

ST_ASTEXT(ST_CENTROID(ST_GEOMFROMTEXT('MULTIPOINT((-15-15),(55)) 
---------------------------------------------------------------- 
POINT (-5 -5) 

SELECT ST_AsText(ST_Centroid(st_geomfromtext('MULTILINESTRING ZM ((0 0 0 0, 2.09887763434 3.9000000000000009 0 0), (1 1 0 0, 2 2 0 0))', 4326),true), 0) FROM dual;

ST_ASTEXT(ST_CENTROID(ST_GEOMFROMTEXT('MULTILINESTRINGZM((0000,2 
---------------------------------------------------------------- 
POINT (1 2)                                                     
```
