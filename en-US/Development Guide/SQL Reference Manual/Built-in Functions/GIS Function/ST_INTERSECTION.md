```ebnf
st_intersection = ST_INTERSECTION "(" geometry1 "," geometry2 [ ","girdsize]")".
```

The ST_INTERSECTION function returns the intersection of two geometry objects, with the return value being of type ST_GEOMETRY.

The function will project the geometry objects onto the grid lines for calculations and return the result.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same Spatial Reference Identifier (SRID).

**gridsize**

The data type of gridsize is DOUBLE, representing the size of the grid lines used during function calculations. If omitted, it defaults to -1.

This function adheres to the following rules:

* When geometry1 is completely contained within geometry2, the function returns geometry1.
* When both geometry1 and geometry2 are EMPTY, the function returns geometry2.
* When either geometry1 or geometry2 is EMPTY, the function returns the EMPTY of that empty object type.
* When any input parameter is NULL, the function returns NULL.
* When any input parameter contains Nan, the function returns an error.
* 3D coordinates are supported, but the function will ignore the Z coordinate for calculations.

***Example*** for Heap tables

```sql
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POLYGON((2 0, 0 2, 0 0, 2 0))'), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((2 0, 0 0, 1 1, 2 0))  

--Returns geometry1 when geometry1 is completely contained within geometry2
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POINT(1 1)'), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT (1 1)    

--Returns geometry2 when both geometry1 and geometry2 are EMPTY
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POINT EMPTY'), ST_GEOMFROMTEXT('POLYGON EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY  

--Returns the EMPTY of that empty object type when either geometry1 or geometry2 is EMPTY
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POINT EMPTY'), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT EMPTY   

--Returns NULL when any input parameter is NULL
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POLYGON((2 0, 0 2, 0 0, 2 0))'), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))'),NULL), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
                                                                

SELECT ST_ASTEXT(ST_INTERSECTION(NULL, ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
 
--Returns an error when SRIDs are different
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POLYGON((2 0, 0 2, 0 0, 2 0))',9), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))',10)), 0) res FROM DUAL;

YAS-07202 plugin execution error, Operation on mixed SRID geometries: 9 != 10

--Returns an error when parameters contain Nan
SELECT ST_ASTEXT(ST_INTERSECTION(ST_GEOMFROMTEXT('POLYGON((2 0, 0 Nan, 0 0, 2 0))'), ST_GEOMFROMTEXT('POLYGON((2 2, 2 0, 0 0, 2 2))')), 0) res FROM DUAL;

YAS-07202 plugin execution error, POLYGON has invalid coordinate
```
