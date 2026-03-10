```ebnf+diagram
st_asgeojson::= ST_ASGEOJSON "(" geometry ["," precision ] ["," options ] ")"
```

ST_ASGEOJSON函数根据输入的geometry，返回该geometry的GeoJSON表示，GeoJSON是一种使用JavaScript对象符号(JSON)编码各种地理数据结构的格式。只支持二维几何图形，三维几何图形会丢弃z轴坐标。输出的GeoJSON不包含坐标参考系。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**precision**

precision数据类型为INT，该参数用于语法兼容，无实际含义。

*   支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。
*   该参数可以省略。

**options**

options的数据类型是INT，该参数用于语法兼容，无实际含义。

*   支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。
*   该参数可以省略，如该函数仅有2个参数时默认省略options。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
-- empty
SELECT ST_AsGeoJSON(ST_GeomFromText('POINT EMPTY')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
{"type":"Point","coordinates":[]} 

-- precision
SELECT ST_AsGeoJSON(ST_GeomFromText('POINT(1.1111111 1.1111111)', 4326), -2) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
{"type":"Point","coordinates":[1.1111111,1.1111111]}    

-- options
SELECT ST_AsGeoJSON(ST_GeomFromText('LINESTRING(1 1, 2 2, 3 3, 4 4)'), 0, 1) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
{"type":"LineString","coordinates":[[1.0,1.0],[2.0,2.0],[3.0,3.0],[4.0,4.0]]}
```
