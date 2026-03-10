```ebnf+diagram
st_containsproperly::= ST_CONTAINSPROPERLY "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_CONTAINSPROPERLY function is to determine whether geometry1 completely contains geometry2. It returns TRUE if geometry1 completely contains geometry2; otherwise, it returns FALSE. Unlike [ST_CONTAINS](ST_CONTAINS), ST_CONTAINS(A,A) = TRUE, while ST_CONTAINSPROPERLY(A,A) = FALSE.

Geometry1 completely contains geometry2 means that there are no points in geometry2 that are outside or on the boundary of geometry1, i.e., all points in geometry2 are inside geometry1.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

Input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

This function adheres to the following rules:

* When any input parameter is NULL, the function returns NULL.
* If any input geometry is EMPTY, the function returns FALSE.
* Only 2D results are calculated; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates for calculation.
* The result's precision is 15 decimal places; precision beyond this is not guaranteed.
* It follows the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.
* This function supports the use of RTree indexes.

***Example*** for Heap tables

```sql
-- The POLYGON completely contains the points in the LINESTRING
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), ST_GeomFromText('LINESTRING(3 3, 2 3)')) res FROM DUAL;

RES
-------------------- 
true                
       
-- The LINESTRING has one point intersecting with the POLYGON
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), ST_GeomFromText('LINESTRING(3 3, 4 4)')) res FROM DUAL;

RES
-------------------- 
false

-- Input contains NULL
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), NULL) res FROM DUAL;

RES
--------------------
```
