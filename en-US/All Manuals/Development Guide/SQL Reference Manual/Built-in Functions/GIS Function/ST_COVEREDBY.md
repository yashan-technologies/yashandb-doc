```ebnf+diagram
st_coveredby::= ST_COVEREDBY "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_COVEREDBY function is to determine whether geometry2 covers geometry1. It returns TRUE if there are no points of geometry1 located outside of geometry2; otherwise, it returns FALSE.

**geometry**

[General expression](../../General SQL Syntax/expr), which must be a valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference System Identifier (SRID).

This function adheres to the following rules:

* When any of the input parameters is NULL, the function returns NULL.
* Only 2D results are computed; Z coordinates in the input parameters are ignored during the computation.
* The guaranteed precision is 15 decimal places; results are not guaranteed beyond 15 decimal places.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data according to the given WKT and SRID
SELECT ST_CoveredBy(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
false               
                                          
SELECT ST_CoveredBy(ST_GeomFromText('POINT(3 3)'), ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_CoveredBy(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
