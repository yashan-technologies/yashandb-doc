```ebnf
st_within = ST_WITHIN "(" geometry1 "," geometry2 ")".
```

The functionality of the ST_WITHIN function is to determine whether geometry1 is completely inside geometry2. If it is, it returns TRUE; otherwise, it returns FALSE.

Geometry1 is considered to be inside geometry2 if and only if no points of geometry1 lie outside of geometry2 and at least one point of the interior of geometry1 lies inside geometry2.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometries, geometry1 and geometry2, must have the same spatial reference system identifier (SRID).

This function adheres to the following rules:

* ST_WITHIN is the inverse of ST_CONTAINS. Therefore, ST_WITHIN(A,B) = ST_CONTAINS(B,A).
* If any input parameter is NULL, the function returns NULL.
* Only 2D results are computed; if the input parameters include Z coordinates, the function will ignore Z coordinates for computation.
* The guaranteed precision is up to 15 decimal places; results are not guaranteed beyond 15 decimal places.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
--ST_GEOMFROMTEXT function will return an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Within(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
false                
                                               
SELECT ST_Within(ST_GeomFromText('POINT(3 3)'), ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))')) res FROM DUAL;

RES
-------------------- 
true                
    
SELECT ST_Within(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
