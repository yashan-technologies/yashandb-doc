```ebnf+diagram
st_maxdistance::= ST_MAXDISTANCE "(" geometry1 "," geometry2 ")"
```

The ST_MAXDISTANCE function returns the two-dimensional maximum distance corresponding to the input geometry1 and geometry2.

**geometry**

[General Expression](../../General SQL Syntax/expr), the value must be valid ST_GEOMETRY type data, and must follow the rules below:

* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be equal, otherwise an error will be thrown.

This function adheres to the following rules:

* When the input parameters contain NULL or EMPTY, the function returns NULL.
* Only 2D results are calculated; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates in the calculation.

***Example*** for Heap tables

```sql
SELECT ST_MaxDistance(ST_GeomFromText('linestring(-72.1523 42.6343, -72.4524 42.2872)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.1235 42.3521)',4326)) res FROM dual;

RES
----------- 
3.508E-001

SELECT ST_MaxDistance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)) res FROM dual;

RES
----------- 
1.975E-001
```
