```ebnf+diagram
st_concavehull::= ST_CONCAVEHULL"(" geometry ", "ratio"[, "allow_holes"])"
```

The ST_CONCAVEHULL function is used to calculate the concave hull of a Geometry object. The concave hull is a geometric object that can cover all the vertices of the input geometric object, which is generally a concave polygon.

This function follows these rules:

- If the input is a point or multiple identical points, the result will still be a point.
- If the input is multiple collinear points, the result will be a line segment.
- If the input consists of two or more collinear line segments, the result will be a line segment.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

**ratio**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), used to control the length ratio between the shortest and longest edges, thus affecting the concavity of the hull. The type is DOUBLE, with a valid range of [0,1.0].

- The construction process of the concave hull uses the principles of Delaunay triangulation, continuously removing the longest outer edges to achieve the optimal result.
- When the ratio is 1, the result is a convex hull; when the ratio is 0, the result is the concave hull with the greatest concavity; when the ratio is between 0 and 1, the larger the ratio, the greater the concavity.

**allow_holes**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), this parameter is of type bool, with a default value of false, indicating whether to allow holes in the returned polygon.

***Example*** for Heap tables

```sql
SELECT st_astext(st_concavehull(st_geomfromtext('point(0 0)'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POINT (0 0)

SELECT st_astext(st_concavehull(st_geomfromtext('multipoint(0 0, 1 1)'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 1 1)

SELECT st_astext(st_concavehull(st_geomfromtext('multipoint(1 1, 1 1)'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POINT (1 1)

SELECT st_astext(st_concavehull(st_geomfromtext('linestring(0 0, 1 0, 1 1)'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POLYGON ((0 0, 1 1, 1 0, 0 0))

SELECT st_astext(st_concavehull(st_geomfromtext('geometrycollection(multipoint(0 0, 1 1), multipoint(2 2, 3 3))'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 3 3)

SELECT st_astext(st_concavehull(st_geomfromtext('geometrycollection(linestring(0 0, 1 1), linestring(2 2, 3 3))'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 3 3)

SELECT st_astext(st_concavehull(st_geomfromtext('geometrycollection(polygon((0 0, 1 0, 1 1, 0 1, 0 0)), polygon((10 10, 20 10, 20 20, 10 20, 10 10)))'), 1, 0), 0) FROM dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POLYGON ((1 0, 0 0, 0 1, 10 20, 20 20, 20 10, 1 0))
```
