```ebnf
st_makeline = ST_MAKELINE "(" geometry1 "," geometry2  ")".
```

The ST_MAKELINE function returns a LINESTRING data connected by the points that compose the input geometry1 and geometry2 in order.

**geometry**

[General Expression](../../General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data, following these rules:

- The input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).
- Geometry1 and geometry2 must be one of the POINT, LINESTRING, or MULTIPOINT types.

When the input parameters contain NULL, the function returns NULL.

***Example*** for Heap tables

```sql
--ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('POINT(1 1)'), ST_GeomFromText('POINT(2 2)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 2 2)

SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('LINESTRING(1 1, 2 2)'), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 2 2, 3 3)

SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('MULTIPOINT(1 1 1, 2 2 2)'), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING Z (1 1 1, 2 2 2, 3 3 0)

-- An error is returned when the SRID of geometry1 and geometry2 do not match
SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('MULTIPOINT(1 1 1, 2 2 2)',3), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

YAS-07202 plugin execution error, different srid in st_makeline
```
