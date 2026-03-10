```ebnf+diagram
st_linemerge::= ST_LINEMERGE "(" geometry "[,"directed"])"
```

The ST_LINEMERGE function is used to combine lines within a MultiLineString into a LineString or MultiLineString.

This function adheres to the following rules:

- When the input parameter is Point, MultiPoint, Polygon, or MultiPolygon, the function returns a GeometryCollection Empty.
- When the input parameter is NULL, the function returns NULL.
- When the input parameter is an empty LineString or MultiLineString, the function returns LineString Empty or MultiLineString Empty.
- When the input parameter is points or multipoints, it returns NULL.
- Only when two lines in the input MultiLineString intersect and the degree of the intersection point is 2 (i.e., intersecting at the endpoints), will the two lines be merged.
- The geometry object returned by the function will have the same SRID as the input geometry object.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**directed**

[General expression](../../General SQL Syntax/expr), its type is bool, with a default value of false. When directed is true, the input LineString (or LineStrings contained in MultiLineString) is treated as directed lines; otherwise, it is treated as undirected. Directed lines with opposite directions cannot be combined.

***Example*** for Heap tables

```sql
-- When directed = true, two lines in opposite directions intersect at endpoints, but will not merge
SELECT st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 1), (2 2, 1 1))'), true), 0) st_linemerge FROM dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1), (2 2, 1 1))

-- When directed = false, two lines in opposite directions intersect at endpoints, and will merge
SELECT st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 1), (2 2, 1 1))'), false), 0) st_linemerge FROM dual;

ST_LINEMERGE
----------------------------------------------------------------
LINESTRING (0 0, 1 1, 2 2)

-- The intersection point (2 0) is a point inside the first line, and after the intersection, the degree of (2 0) is 3, so they will not merge
SELECT st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 2 0, 4 0), (2 0, 2 1))')), 0) st_linemerge FROM dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 2 0, 4 0), (2 0, 2 1))

-- Three lines intersect at (1 0), resulting in the degree of that point being 3, so this point cannot act as the intersection point, and the three lines will not merge
SELECT st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 0), (1 0, 2 0), (1 0, 1 1))')), 0) st_linemerge FROM dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 0), (1 0, 2 0), (1 0, 1 1))
```
