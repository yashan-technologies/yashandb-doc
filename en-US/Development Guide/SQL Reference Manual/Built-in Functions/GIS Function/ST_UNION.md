```ebnf
st_union = ST_UNION "(" geometry1 "," geometry2 [ ","girdsize]")".
```

The ST_UNION function returns the union of two geometry objects, with the return value being of ST_GEOMETRY type.

The function will project the geometry objects onto the grid lines for calculations and return the result.

**geometry**

[Common expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The input geometry1 and geometry2 must have the same spatial reference identifier (SRID).

**gridsize**

The data type of gridsize is DOUBLE, which represents the grid line size used during function calculations; omitted defaults to -1.

This function follows the rules below:

* When both geometry1 and geometry2 are EMPTY, the function returns geometry1.
* When either geometry1 or geometry2 is EMPTY, the function returns the non-empty object.
* When any input parameter is NULL, the function returns NULL.
* When any input parameter contains NaN, the function returns an error.
* 3D coordinates are supported as input, but the function will ignore the Z coordinate during calculations.

***Example*** for Heap tables

```sql
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0.000000000000000 5.000000000000000, 0.375000000000000 5.000000000000000, 0.000000000000000 8.000000000000000, 8.000000000000000 8.000000000000000, 8.000000000000000 0.000000000000000, 5.000000000000000 0.000000000000000, 1.000000000000000 0.000000000000000, 0.000000000000000 0.000000000000000, 0.000000000000000 5.000000000000000))

-- Returns geometry1 when both geometry1 and geometry2 are EMPTY
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON EMPTY'),ST_GEOMFROMTEXT('LINESTRING EMPTY'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY  

-- Returns the non-empty object when one of geometry1 and geometry2 is EMPTY
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('LINESTRING EMPTY'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0.000000000000000 0.000000000000000, 0.000000000000000 5.000000000000000, 5.000000000000000 5.000000000000000, 5.000000000000000 0.000000000000000, 0.000000000000000 0.000000000000000))

-- Returns NULL when a parameter is NULL
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),NULL)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 

SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'),NULL)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
 
-- Returns an error when SRIDs are different
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 5,5 5,5 0,0 0))',6),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))',1))) res FROM DUAL;

YAS-07202 plugin execution error, Operation on mixed SRID geometries: 6 != 1

-- Returns an error when parameters contain NaN
SELECT ST_ASTEXT(ST_UNION(ST_GEOMFROMTEXT('POLYGON((0 0,0 Nan,5 5,5 0,0 0))'),ST_GEOMFROMTEXT('POLYGON((1 0,0 8,8 8,8 0,1 0))'))) res FROM DUAL;

YAS-07202 plugin execution error, POLYGON has invalid coordinate
```
