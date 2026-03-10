```ebnf+diagram
st_multi::= ST_Multi "(" geometry ")"
```

The ST_MULTI function returns the Geometry Collection type corresponding to the input Geometry object. If the input Geometry is already of collection type, it returns the input Geometry object.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The correspondence between input Geometry types and return Geometry types is shown in the following table:

|Input Type |Output Type |
|--------|----------|
| Point | MultiPoint |
| LineString | MultiLineString|
| Polygon | MultiPolygon |
| MultiPoint | MultiPoint |
| MultiLineString| MultiLineString|
| MultiPolygon | MultiPolygon |
| GeometryCollection | GeometryCollection |

***Example*** for Heap tables

```sql
SELECT st_astext(st_multi(st_geomfromText('point(0 0)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOINT (0 0)

SELECT st_astext(st_multi(st_geomfromText('linestring(0 0, 1 1)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1))

SELECT st_astext(st_multi(st_geomfromText('polygon((0 0, 1 1, 2 0, 0 0))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOLYGON (((0 0, 1 1, 2 0, 0 0)))

SELECT st_astext(st_multi(st_geomfromText('multipoint(0 0, 1 1)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOINT (0 0, 1 1)

SELECT st_astext(st_multi(st_geomfromText('multilinestring((0 0, 1 1),(2 2, 4 4))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1), (2 2, 4 4))

SELECT st_astext(st_multi(st_geomfromText('multipolygon(((0 0, 1 1, 2 0, 0 0)), ((10 0, 11 1, 12 0, 10 0)))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOLYGON (((0 0, 1 1, 2 0, 0 0)), ((10 0, 11 1, 12 0, 10 0)))

SELECT st_astext(st_multi(st_geomfromText('geometrycollection(point(0 0), point(1 1))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
GEOMETRYCOLLECTION (POINT (0 0), POINT (1 1))
```
