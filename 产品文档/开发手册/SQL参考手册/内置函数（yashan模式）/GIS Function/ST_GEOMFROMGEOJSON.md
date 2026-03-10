```ebnf+diagram
st_geomfromgeojson::= ST_GEOMFROMGEOJSON "(" geojson ")"
```

ST_GEOMFROMGEOJSON函数根据输入的geojson，返回该geojson对应的ST_GEOMETRY类型数据。只支持二维几何图形，三维几何图形会报错。

**geojson**

geojson的数据类型是CLOB，必须为有效的GeoJSON数据，遵循如下规则：

*   支持能够隐式转换成CLOB的类型。
*   当输入的参数是NULL时，函数返回NULL。
*   输入的数据可以包含外包框、坐标参考系，对此不检查正确性且不参与结果计算。

示例（HEAP表）

```sql
-- empty
SELECT ST_AsText(ST_GeomFromGeoJSON('{"type":"Point","coordinates":[]}')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT EMPTY                                                     

-- point
SELECT ST_AsText(ST_GeomFromGeoJSON(ST_AsGeoJSON(ST_GeomFromText('POINT(1 1)')))) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1.000000000000000 1.000000000000000)

-- geometry collection
SELECT ST_AsText(ST_GeomFromGeoJSON('{"type":"GeometryCollection","geometries":[{"type":"Point","coordinates":[100.0,0.0]},{"type":"LineString","coordinates":[[101.0,0.0],[102.0,1.0]]}],"crs":{"type":"name","properties":{"name":"urn:ogc:def:crs:EPSG::3395"}}}')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
GEOMETRYCOLLECTION (POINT (100.000000000000000 0.000000000000000), LINESTRING (101.000000000000000 0.000000000000000, 102.000000000000000 1.000000000000000))
```