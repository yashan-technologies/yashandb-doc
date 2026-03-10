```ebnf+diagram
st_dwithin::= ST_DWITHIN "(" geometry1 "," geometry2 "," distance ")"
```

The functionality of the ST_DWITHIN function is to determine whether geometry1 and geometry2 are within the specified distance distance. If they are within distance, it returns TRUE; otherwise, it returns FALSE.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometries geometry1 and geometry2 must have the same Spatial Reference System Identifier (SRID).

**distance**

The data type of distance is DOUBLE, which represents the specified distance.

* Supports types that can be implicitly converted to DOUBLE.
* If the input distance is less than 0, an error will be reported.

This function adheres to the following rules:

* When any input parameter is NULL, the function returns NULL.
* If either of the input geometries is EMPTY, the function returns FALSE.
* Only 2D results are calculated; if there are Z coordinates in the input parameters, the function will ignore the Z coordinates for calculations.
* The Spatial Reference System Identifier (SRID) of the geometries must be defined in the spatial_ref_sys system table or be 0; otherwise, an error will be reported.
* If the SRID corresponding to the geometry in spatial_ref_sys has an srs_type of GEOGRAPHY2D or GEOGRAPHY3D, the geographic coordinate algorithm will be used for calculations; otherwise, the projected coordinate algorithm will be used.
* This function supports RTREE indexing but does not recommend using RTREE indexing for this function in latitude and longitude coordinate systems.

***Example*** for Heap tables

```sql
-- Returns the distance between two geometries
SELECT ST_Distance(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)')) res FROM DUAL;

RES
----------- 
5.0E+000

-- The distance between two geometries is within the specified distance
SELECT ST_DWithin(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)'), 5) res FROM DUAL;

RES
-------------------- 
true                

-- The distance between two geometries is not within the specified distance
SELECT ST_DWithin(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)'), 4) res FROM DUAL;

RES
-------------------- 
false
```
