```ebnf+diagram
st_touches::= ST_TOUCHES "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_TOUCHES function is to determine whether two Geometry objects have at least one common point, and their interiors do not intersect. If geometry1 and geometry2 intersect but their interiors do not, it returns TRUE; otherwise, it returns FALSE.

In the case of POINT/POINT inputs, it always returns FALSE because POINT has no boundary.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

This function adheres to the following rules:

* When any input parameter is NULL, the function returns NULL.
* Only 2D results are computed; if there are Z coordinates in the input parameters, the Z coordinates will be ignored during computation.
* The guaranteed precision is up to 15 decimal places; results beyond 15 decimal places are not guaranteed.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

If the DE-9IM intersection matrix of the two Geometries matches the following patterns, then this relationship holds:

* FT*******
* F\*\*T*****
* F\*\*\*T****

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Touches(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
true                

SELECT ST_Touches(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Touches(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
