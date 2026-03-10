```ebnf+diagram
st_overlaps::= ST_OVERLAPS "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_OVERLAPS function is to determine whether two Geometry objects intersect and have the same dimension, but do not fully contain each other. It returns TRUE if geometry1 and geometry2 "spatially overlap"; otherwise, it returns FALSE.

If two Geometry objects have the same dimension, each Geometry must have at least one point that does not belong to the other Geometry (or equivalently, they do not cover each other), and the intersection points within them have the same size, then they are considered overlapping. The overlap relationship is symmetric.

**geometry**

[General Expression](../../General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference System Identifier (SRID).

This function follows the rules below:

* When any input parameter is NULL, the function returns NULL.
* Only 2D results are calculated; if Z coordinates are present in the input parameters, the function will directly ignore the Z coordinates for calculation.
* The guaranteed precision is 15 decimal places; results beyond 15 decimal places are not guaranteed.
* Adheres to DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Overlaps(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_Overlaps(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Overlaps(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
