```ebnf+diagram
st_equals::= ST_EQUALS "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_EQUALS function is to determine whether two geometries contain the same set of points, i.e., the given two geometries are "spatially equal". If they are equal, it returns TRUE; otherwise, it returns FALSE.

Spatial equality means `ST_WITHIN(A, B) = TRUE` and `ST_WITHIN(B, A) = TRUE`, which also indicates that the order of points may differ but represents the same geometric structure.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometries, geometry1 and geometry2, must have the same Spatial Reference System Identifier (SRID).

This function adheres to the following rules:

* When any of the input parameters is NULL, the function returns NULL.
* It only computes 2D results; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates in the calculations.
* The guaranteed precision is up to 15 decimal places; results beyond 15 decimal places are not guaranteed.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Equals(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(1 1, 2 2, 3 3)')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_Equals(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 4 6 ,3 5)')) res FROM DUAL;

RES 
-------------------- 
false               

SELECT ST_Equals(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
