```ebnf
st_intersects = ST_INTERSECTS "(" geometry1 "," geometry2 ")".
```

The functionality of the ST_INTERSECTS function is to determine whether two Geometries intersect (i.e., have at least one point in common); it returns TRUE if they intersect, otherwise it returns FALSE.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometries geometry1 and geometry2 must have the same spatial reference system identifier (SRID).

This function adheres to the following rules:

* If any input parameter is NULL, the function returns NULL.
* Only 2D results are calculated; if the input parameters contain Z coordinates, the function will ignore the Z coordinates during calculation.
* The guaranteed precision is 15 decimal places; results are not guaranteed beyond 15 decimal places.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

If the DE-9IM intersection matrix of the two Geometries matches any of the following cases, the relationship holds:

* T********
* \*T*******
* \*\*\*T*****
* \*\*\*\*T****

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Intersects(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
true                

SELECT ST_Intersects(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 4 6, 3 5)')) res FROM DUAL;

RES 
-------------------- 
false               

SELECT ST_Intersects(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
