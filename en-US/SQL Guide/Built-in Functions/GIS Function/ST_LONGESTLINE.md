```ebnf+diagram
st_longestline::= ST_LONGESTLINE "(" geometry1 "," geometry2 ")"
```

The ST_LONGESTLINE function returns the two-dimensional longest LineString between geometry1 and geometry2 based on the inputs of geometry1 and geometry2.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data, must follow these rules:

* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be equal; otherwise, an error will occur.

This function adheres to the following rules:

* If any input parameter is NULL, the function returns NULL.
* Only 2D results are computed; if there are Z coordinates in the input parameters, the Z coordinates will be ignored in the calculation.
* The returned LineString starts from geometry1 and ends at geometry2.
* If geometry1 and geometry2 intersect, the result is a straight line with the intersection point as both the start and end point.
* The longest LineString always occurs between the two endpoints. If multiple longest LineStrings are found, the first longest LineString will be returned.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_LONGESTLINE(ST_GeomFromText('POLYGON ((7.000 7.000, 6.000 6.000, 5.444 6.169, 7.000 7.000)) '),ST_GeomFromText('POLYGON((6.400 6.600, 6.231 7.156, 63.24 6.983,  6.400 6.600))')),6) res FROM dual;

RES
---------------------------------------------------------------- 
LINESTRING (5.444000 6.169000, 63.240000 6.983000)

SELECT ST_AsText(ST_LONGESTLINE(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)), 6) res FROM dual;

RES
---------------------------------------------------------------- 
LINESTRING (-72.123500 42.352100, -72.123000 42.154600)
```
