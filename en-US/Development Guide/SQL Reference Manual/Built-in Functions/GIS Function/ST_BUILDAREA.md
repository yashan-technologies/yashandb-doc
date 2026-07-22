```ebnf
st_buildarea = ST_BUILDAREA "(" geometry ")".
```

The ST_BUILDAREA function is used to combine lines from the input geometry into a polygon.

This function adheres to the following rules:

- The function operates on 2D spatial objects. When the input geometry is a 3D object, the output remains a 3D object, but the third dimension coordinates do not participate in calculations. That is, when determining if the loop is closed or if there are spatial containment relationships, only the first two coordinates are considered. A loop that is closed in 2D but not closed in 3D is still regarded as closed.
- If the input parameter is NULL, the function returns NULL.
- If the input parameter is an empty geometry object, the function returns an empty polygon.
- If the input parameter is a point or multi-point, it returns NULL.
- If the input parameter is a LineString, it returns a polygon if the LineString is a closed loop, otherwise it returns NULL.
- If the input parameter is a polygon, it returns the polygon.
- If the input parameter is a collection object (MultiLineString, MultiPolygon, GeometryCollection), the function behaves as follows:
  - If executing ST_BuildArea on a member independently returns NULL, that member does not affect the result.
  - If there are containment relationships among multiple members (for example, one loop contains another loop, or one polygon contains another polygon), the contained objects will be treated as holes in the polygon.
- The SRID of the returned geometry object will be consistent with that of the input geometry object.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

***Example*** for Heap tables

```sql
-- Non-closed loop, returns NULL
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0, 4 0, 4 4, 0 4)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------

-- Closed loop returns a polygon formed by the loop
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0, 4 0, 4 4, 0 4, 0 0)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0))

select st_astext(st_buildarea(st_geomfromtext('polygon((0 0, 4 0, 4 4, 0 4, 0 0))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0))

select st_astext(st_buildarea(st_geomfromtext('polygon((0 0, 4 0, 4 4, 0 4, 0 0),(2 2, 3 2, 3 3, 2 3, 2 2))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0), (2 2, 3 2, 3 3, 2 3, 2 2))

-- Two loops exist in a containment relationship, the area enclosed by the inner loop is treated as a hole in the polygon
select st_astext(st_buildarea(st_geomfromtext('multilinestring((0 0, 4 0, 4 4, 0 4, 0 0),(2 2, 3 2, 3 3, 2 3, 2 2))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0), (2 2, 3 2, 3 3, 2 3, 2 2))

-- A closed 2D loop that is not closed in 3D is still viewed as closed, and the output still includes 3D coordinates
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0 0, 4 0 0, 4 4 0, 0 4 0, 0 0 1)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON Z ((0 0 1, 0 4 0, 4 4 0, 4 0 0, 0 0 0))
```
