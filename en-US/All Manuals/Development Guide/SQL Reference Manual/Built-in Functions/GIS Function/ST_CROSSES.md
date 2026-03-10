```ebnf+diagram
st_crosses::= ST_CROSSES "(" geometry1 "," geometry2 ")"
```

The functionality of the ST_CROSSES function is to determine whether two geometries have partially (rather than totally) overlapping interior points.

If their intersection "crosses in space," meaning the two geometries share some (but not all) interior points, it returns TRUE; otherwise, it returns FALSE.

The crossing must meet the following two conditions:

- The intersection of the interiors of the two geometries must be non-empty, and its dimension must be less than the maximum dimension of the two input geometries.
- The intersection of the two geometries cannot equal either of the input geometries.

**geometry**

[Generic Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same spatial reference identifier (SRID).

This function follows these rules:

* When any input parameter is NULL, the function returns NULL.
* Only 2D results are calculated; if any input parameter contains Z coordinates, the function will ignore the Z coordinates during computation.
* The guaranteed precision is up to 15 decimal places; results are not guaranteed beyond 15 decimal places.
* It adheres to the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

If the DE-9IM intersection matrix of two geometries matches the following cases, then this relationship holds:

* T\*T******: for scenarios involving Point/Line, Point/Area, and Line/Area.
* T***\*\*T**: for scenarios involving Line/Point, Area/Point, and Area/Line.
* 0********: for scenarios involving Line/Line.

For scenarios involving Point/Point and Area/Area, it always returns FALSE.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Crosses(ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)'), ST_GeomFromText('LINESTRING(3 5, 4 6 ,3 5)')) res FROM DUAL;

RES 
-------------------- 
true                
                              
SELECT ST_Crosses(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('LINESTRING(3 5, 4 6, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Crosses(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
