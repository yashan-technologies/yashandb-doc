```ebnf+diagram
st_relate::= ST_RELATE "(" geometry1 "," geometry2 [ "," boundaryNodeRule ] ")"
```

The functionality of the ST_RELATE function is to calculate the DE-9IM matrix string that represents the spatial relationship between the two input Geometries based on the input boundary value rule (boundaryNodeRule).

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

**boundaryNodeRule**

The boundaryNodeRule represents the boundary value rule, this parameter is of INTEGER type, supporting data that can be implicitly converted to INTEGER type. The default value is 1. The optional parameters are the following four (entering other values will result in an error):

* 1: OGC/MOD2. 
* 2: Endpoint. 
* 3: MultivalentEndpoint. 
* 4: MonovalentEndpoint. 

This function adheres to the following rules:

* When any input parameter is NULL, the function returns NULL.
* Only 2D results are computed; if the input parameters contain Z coordinates, the function will ignore the Z coordinates for the calculation.
* The guaranteed precision is 15 decimal places. Results are not guaranteed beyond 15 decimal places.
* Adheres to the DE-9IM (Dimensionally Extended 9-Intersection Model) rules.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_Relate(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
1F100F102                                                       

SELECT ST_Relate(ST_GeomFromText('POINT(1 2)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
FF0FFF102                                                       

SELECT ST_Relate(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
                                                                
```
