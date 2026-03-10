```ebnf+diagram
st_covers::= ST_COVERS "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_COVERS function is to determine whether geometry1 covers geometry2, meaning it returns TRUE if no points of geometry2 are outside of geometry1; otherwise, it returns FALSE.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

The input geometries geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

This function adheres to the following rules:

* When any input parameter is NULL, the function returns NULL.
* Only 2D results are computed; if any input parameter includes Z coordinates, the function will ignore the Z coordinates for calculations.
* The guaranteed precision is 15 decimal places; results beyond 15 decimal places are not guaranteed.
* Adheres to the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
--The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Covers(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
true                
                                          
SELECT ST_Covers(ST_GeomFromText('POINT(3 3)'), ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))')) res FROM DUAL;

RES 
-------------------- 
false               

SELECT ST_Covers(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
