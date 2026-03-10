```ebnf+diagram
st_geomfromgeojson::= ST_GEOMFROMGEOJSON "(" geojson ")"
```

The ST_GEOMFROMGEOJSON function returns the ST_GEOMETRY type data corresponding to the input geojson. It only supports two-dimensional geometries; three-dimensional geometries will result in an error.

**geojson**

The data type of geojson is CLOB, and it must be valid GeoJSON data, adhering to the following rules:

* Supports types that can be implicitly converted to CLOB.
* When the input parameter is NULL, the function returns NULL.
* The input data may contain a bounding box and coordinate reference system, but these are not checked for correctness and do not participate in result calculation.

***Example*** for Heap tables

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
