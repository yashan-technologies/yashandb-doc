```ebnf
st_contains = ST_CONTAINS "(" geometry1 "," geometry2 ")".
```

The functionality of the ST_CONTAINS function is to determine whether geometry1 contains geometry2. It returns TRUE if it does, and FALSE otherwise.

Geometry1 contains geometry2 if and only if no points of geometry2 are located outside of geometry1, and at least one point of the interior of geometry2 is located within the interior of geometry1.

**geometry**

[General Expression](../../General SQL Syntax/expr), which must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

This function adheres to the following rules:

* ST_CONTAINS is the inverse of ST_WITHIN. Therefore, ST_CONTAINS(A,B) = ST_WITHIN(B,A). 
* If any input parameters are NULL, the function returns NULL. 
* Only 2D results are calculated; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates in the calculation. 
* The guaranteed precision is up to 15 decimal places; results beyond 15 decimal places are not guaranteed.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data type based on the provided WKT and SRID.
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
true                
                                              
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES 
-------------------- 
false                
    
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
