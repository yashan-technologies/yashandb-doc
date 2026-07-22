```ebnf
st_pointonsurface = ST_PointOnSurface"(" geometry ")".
```

The ST_POINTONSURFACE function is used to obtain a point located inside the surface.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid data of ST_GEOMETRY type.

***Example*** for Heap tables

```sql
select ST_AsText(ST_PointOnSurface(st_geomfromtext('MULTIPOINT((-15 -15), (5 5), EMPTY)')), 0) from dual;

ST_ASTEXT(ST_POINTONSURFACE(ST_GEOMFROMTEXT('MULTIPOINT((-15-15) 
---------------------------------------------------------------- 
POINT (-15 -15)
```
