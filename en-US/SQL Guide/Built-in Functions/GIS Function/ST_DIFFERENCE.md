```ebnf+diagram
st_difference::= ST_DIFFERENCE "(" geometry1 "," geometry2 [ "," girdsize]")"
```

The ST_DIFFERENCE function returns a geometry that contains geometry1 but not geometry2. The return value is of the ST_GEOMETRY type.

The function will project the geometry objects onto the grid lines for calculation and return the result.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same spatial reference system identifier (SRID).

**gridsize**

The data type of gridsize is DOUBLE, indicating the size of the grid lines used during function calculation. If omitted, it defaults to -1.

This function adheres to the following rules:

* The output content is strictly determined by the order of the input parameters, and the return value must be related to geometry1.
* When geometry1 is completely contained within geometry2, the function returns an EMPTY type of geometry1.
* When both geometry1 and geometry2 are EMPTY or any one of them is EMPTY, the function returns geometry1.
* When there is a NULL in the input parameters, the function returns NULL.
* When the input parameters contain NaN, the function returns an error.
* 3D coordinates are supported, but the function will ignore the Z coordinate during calculations.

***Example*** for Heap tables

```sql
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
MULTILINESTRING ((0 20, 0 30), (0 60, 0 80)) 

-- Returns EMPTY type of geometry1 when geometry2 completely contains geometry1
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 30)'),ST_GEOMFROMTEXT('POLYGON((0 0, 0 60,60 60, 60 0,0 0))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY  

-- Returns geometry1 when both geometry1 and geometry2 are EMPTY
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING EMPTY'),ST_GEOMFROMTEXT('POLYGON EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY 

-- Returns geometry1 when either geometry1 or geometry2 is EMPTY
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING EMPTY'),ST_GEOMFROMTEXT('POLYGON((0 0, 0 60,60 60, 60 0,0 0))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING EMPTY    

SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 30)'),ST_GEOMFROMTEXT('POLYGON EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING (0 20, 0 30)     

-- Returns NULL when gridsize is NULL
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)'),NULL), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 

-- Returns NULL when there is a NULL in the parameters
SELECT ST_ASTEXT(ST_DIFFERENCE(NULL,ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 

-- Returns error when SRIDs are not the same
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 20, 0 80)',10),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)',6)), 0) res FROM DUAL;

YAS-07202 plugin execution error, Operation on mixed SRID geometries: 10 != 6

-- Returns error when parameters contain NaN
SELECT ST_ASTEXT(ST_DIFFERENCE(ST_GEOMFROMTEXT('LINESTRING(0 Nan, 0 80)'),ST_GEOMFROMTEXT('LINESTRING(0 30, 0 60)')), 0) res FROM DUAL;

YAS-07202 plugin execution error, LINESTRING has invalid coordinate
```
