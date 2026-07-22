```ebnf
st_makeenvelope = ST_MakeEnvelope "("xmin", "ymin", "xmax", "ymax"["srid"])".
```

The ST_MAKEENVELOPE function returns a bounding rectangle constructed based on the input minimum and maximum X and Y values. The result is of ST_Geometry type Polygon.

This function follows these rules:

- If any of xmin, ymin, xmax, ymax is NULL, the result is NULL.
- If srid is NULL, the result is NULL.

**xmin**

[General Expression](../../General SQL Syntax/expr), type is double.

**ymin**

[General Expression](../../General SQL Syntax/expr), type is double.

**xmax**

[General Expression](../../General SQL Syntax/expr), type is double.

**ymax**

[General Expression](../../General SQL Syntax/expr), type is double.

**srid**

[General Expression](../../General SQL Syntax/expr), type is integer, default value is 0.

***Example*** for Heap tables

```sql
select st_astext(st_MakeEnvelope(1,2,3,4,4326), 0) geom from dual;

GEOM
----------------------------------------------------------------
POLYGON ((1 2, 3 2, 3 4, 1 4, 1 2))

select st_srid(st_MakeEnvelope(1,2,3,4,4326))  srid from dual;

SRID
------------
4326

select st_srid(st_MakeEnvelope(1,2,3,4)) srid from dual;

SRID
------------
0

select st_astext(st_MakeEnvelope(1,2,3,4,null), 0) geom from dual;

GEOM
----------------------------------------------------------------

select st_astext(st_MakeEnvelope(1,2,3,null), 0) geom from dual;

GEOM
----------------------------------------------------------------

```
