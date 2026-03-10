```ebnf+diagram
st_split::= ST_SPLIT "(" input "," blade ")"
```

The ST_SPLIT function returns the geometric objects produced after the input geometric object is cut by the blade geometric object.

This function adheres to the following rules:

- The input parameters input and blade must have the same spatial reference system; otherwise, the function returns an error.
- If either input or blade is NULL, the function returns NULL.
- If either input or blade contains NAN or INF in its coordinates, the function returns an error.
- The input parameter input can only be LineString, MultiLineString, Polygon, MultiPolygon, or GeometryCollection.
  - If input is LineString, blade can be Point, MultiPoint, LineString, MultiLineString, Polygon, or MultiPolygon.
  - If input is Polygon, blade can only be MultiLineString or LineString.
  - If input is GeometryCollection, it will attempt to cut each member in the GeometryCollection sequentially; if any member does not support cutting, an error is returned.

**input**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**blade**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

***Example*** for Heap tables

```sql
SELECT st_astext(st_split(st_geomfromtext('linestring(1 1, 3 3)'), st_geomfromtext('point(2 2)')), 0) split FROM dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (1 1, 2 2), LINESTRING (2 2, 3 3))

SELECT st_astext(st_split(st_geomfromtext('linestring(1 1, 4 4)'), st_geomfromtext('multipoint(2 2, 3 3)')), 0) split FROM dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (3 3, 4 4), LINESTRING (1 1, 2 2), LINESTRING (2 2, 3 3))

SELECT st_astext(st_split(st_geomfromtext('linestring(1 1, 4 4)'), st_geomfromtext('polygon((0 0, 0 2, 2 2, 2 0, 0 0))')), 0) split FROM dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (1 1, 2 2), LINESTRING (2 2, 4 4))

SELECT st_astext(st_split(st_geomfromtext('polygon((0 0, 0 2, 2 2, 2 0, 0 0))'), st_geomfromtext('linestring(1 -1, 1 3)')), 0) split FROM dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (POLYGON ((0 0, 0 2, 1 2, 1 0, 0 0)), POLYGON ((1 2, 2 2, 2 0, 1 0, 1 2)))

SELECT st_astext(st_split(st_geomfromtext('geometrycollection(polygon((0 0, 0 2, 2 2, 2 0, 0 0)), linestring(0 0, 2 2))'), st_geomfromtext('linestring(1 -1, 1 3)')), 0) split FROM dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (POLYGON ((0 0, 0 2, 1 2, 1 0, 0 0)), POLYGON ((1 2, 2 2, 2 0, 1 0, 1 2)), LINESTRING (0 0, 1 1), LINESTRING (1 1, 2 2))
```
