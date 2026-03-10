```ebnf+diagram
st_asgeojson::= ST_ASGEOJSON "(" geometry ["," precision ] ["," options ] ")"
```

The ST_ASGEOJSON function returns the GeoJSON representation of the input geometry. GeoJSON is a format for encoding a variety of geographic data structures using JavaScript Object Notation (JSON). Only two-dimensional geometries are supported; three-dimensional geometries will lose their z-coordinate. The output GeoJSON does not include a coordinate reference system.

**geometry**

[Generic Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) whose value must be valid ST_GEOMETRY type data.

**precision**

The precision data type is INT. This parameter is for syntactic compatibility and has no actual meaning.

*   Types that can be implicitly converted to INT are supported. If a decimal is input, it will be rounded.
*   This parameter is optional.

**options**

The options data type is INT. This parameter is for syntactic compatibility and has no actual meaning.

*   Types that can be implicitly converted to INT are supported. If a decimal is input, it will be rounded.
*   This parameter can be omitted. If the function has only 2 parameters, options will be omitted by default.

When any of the input parameters is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
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
