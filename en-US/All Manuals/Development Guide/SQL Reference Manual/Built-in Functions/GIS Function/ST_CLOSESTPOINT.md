```ebnf+diagram
st_closestpoint::= ST_CLOSESTPOINT "(" geometry1 "," geometry2 ")"
```

The ST_CLOSESTPOINT function returns the closest 2D point on geometry1 to geometry2 based on the input geometry1 and geometry2. This point is not necessarily an endpoint of the input geometries.

**geometry**

[General Expression](../../General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data, adheres to the following rules:

* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be equal; otherwise, an error will occur.

This function follows these rules:

* If any input parameter is NULL, the function returns NULL.
* Only 2D results are calculated; if Z coordinates are present in the input parameters, the Z coordinates will be ignored for the calculation.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_ClosestPoint(ST_GeomFromText('POLYGON ((7.000 7.000, 6.000 6.000, 5.444 6.169, 7.000 7.000)) '),ST_GeomFromText('POLYGON((6.400 6.600, 6.231 7.156, 63.24 6.983,  6.400 6.600))')),6) res FROM dual;

RES
---------------------------------------------------------------- 
POINT (7.000000 7.000000)

SELECT ST_AsText(ST_ClosestPoint(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)), 6) res FROM dual;

RES
---------------------------------------------------------------- 
POINT (-72.123500 42.352100)
```
