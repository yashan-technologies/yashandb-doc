```ebnf+diagram
st_boundary::= ST_BOUNDARY "(" geometry ")"
```

The ST_BOUNDARY function is used to compute the combined boundary of the input geometry, returning a value of ST_GEOMETRY type data.

This function returns different boundary types based on the data type of the input geometry:

- For POINT and MULTIPOINT types, the boundary is empty, returning a value of GEOMETRYCOLLECTION EMPTY.
- For LINESTRING and MULTILINESTRING types, the boundary consists of endpoints, returning a value of MULTIPOINT type; if the input is a closed loop, it returns MULTIPOINT EMPTY.
- For POLYGON and MULTIPOLYGON types, the boundary consists of linear rings that separate the exterior and interior, returning a value of LINESTRING type.

For LINESTRING and MULTILINESTRING type inputs, this function only calculates the x and y coordinates, with the z coordinate not participating in the calculation. If the x and y coordinates of the input endpoints are equal, it returns MULTIPOINT EMPTY; the output time points are ordered by the x coordinates from negative to positive, followed by the y coordinates from negative to positive, and the output values include the z coordinate.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data but cannot be of GEOMETRY COLLECTION type.

If any input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_BOUNDARY function returns an ST_GEOMETRY data based on the given GEOMETRY
-- Input value is of POINT type
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText('POINT(2 6 9 5)'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY  

-- Input value is of LINESTRING type
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText('LINESTRING(1 1, 0 0, -1 1)')),0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
MULTIPOINT (1 1, -1 1) 

-- Input value is of POLYGON type
SELECT ST_AsText(ST_Boundary(ST_GeomFromText('POLYGON((1 1, 0 0, -1 1, 1 1))')),0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 0 0, -1 1, 1 1)  

-- Parameter contains NULL
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText(null, 4316))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
              
```
