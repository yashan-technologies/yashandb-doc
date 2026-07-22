```ebnf
st_disjoint = ST_DISJOINT "(" geometry1 "," geometry2 ")".
```

The functionality of the ST_DISJOINT function is to determine whether two geometries do not intersect (have no common points); it returns TRUE if they do not intersect, otherwise, it returns FALSE.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometries, geometry1 and geometry2, must have the same Spatial Reference Identifier (SRID).

This function adheres to the following rules:

* If any input parameters are NULL, the function returns NULL.
* Only 2D results are computed; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates during the computation.
* The precision that can be guaranteed is up to 15 decimal places; results beyond 15 decimal places are not guaranteed.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_DisJoint(ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)'), ST_GeomFromText('LINESTRING(3 5, 4 6 ,3 5)')) res FROM DUAL;

RES 
-------------------- 
false               

SELECT ST_DisJoint(ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)'), ST_GeomFromText('LINESTRING(1 1, 2 2 ,3 3)')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_DisJoint(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
