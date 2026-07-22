```ebnf
st_shortestline = ST_SHORTESTLINE "(" geometry1 "," geometry2 ")".
```

The ST_SHORTESTLINE function returns a 2D shortest LineString between geometry1 and geometry2 based on the input values. The two endpoints of the shortest LineString are not necessarily the endpoints of the input geometries.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data and must follow these rules:

* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be equal, otherwise an error will occur.

This function adheres to the following rules:

* If there are NULL parameters, the function returns NULL.
* Only 2D results are calculated; if the input parameters contain Z coordinates, the function will ignore the Z coordinates for the calculation.
* The returned LineString starts from geometry1 and ends at geometry2.
* If geometry1 and geometry2 intersect, the result is a straight line with the intersection point as both the start and end point.

***Example*** for Heap tables

```sql
select ST_AsText(ST_ShortestLine(ST_GeomFromText('POLYGON ((7.000 7.000, 6.000 6.000, 5.444 6.169, 7.000 7.000)) '),ST_GeomFromText('POLYGON((6.400 6.600, 6.231 7.156, 63.24 6.983,  6.400 6.600))')),6) res from dual;

RES
---------------------------------------------------------------- 
LINESTRING (7.000000 7.000000, 7.000000 7.000000)               

select ST_AsText(ST_ShortestLine(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)), 6) res from dual;

RES
---------------------------------------------------------------- 
LINESTRING (-72.123500 42.352100, -72.124396 42.352094)
```
