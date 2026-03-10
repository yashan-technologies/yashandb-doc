```ebnf+diagram
st_envelope::= ST_ENVELOPE "(" geometry ")"
```

The ST_ENVELOPE function is used to compute the minimum bounding rectangle of the input geometry, with the return value being of ST_GEOMETRY type.

The function returns different data types based on the input geometry:

- When the input is of POINT type, the return value is of POINT type.
- For other types, the return value is of POLYGON type. For vertical or horizontal lines, the return value is a POLYGON type representing a linear shape.
- When the input is of any type EMPTY, the return value is POINT EMPTY.

**geometry**

[General expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) which must be a valid ST_GEOMETRY type data, but cannot be of three-dimensional ST_GEOMETRY type data.

If there are NULL parameters in the input, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_ENVELOPE function returns an ST_GEOMETRY data based on the given GEOMETRY
-- Input value is of POINT type
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('POINT(4 9)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT (4 9)  

-- Input value is of MULTIPOINT type
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('MULTIPOINT(4 9,3 12)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((3 9, 4 9, 4 12, 3 12, 3 9)) 

-- Input value is a vertical line
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('LINESTRING(1 2,1 3,1 6)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((1 2, 1 2, 1 6, 1 6, 1 2))    

-- Input value is LINESTRING EMPTY
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('LINESTRING EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT EMPTY 

-- Input value is of GEOMETRY COLLECTION type
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GeomFromText('GEOMETRYCOLLECTION (LINESTRING(55 75,125 150), POINT(20 80))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((20 75, 125 75, 125 150, 20 150, 20 75))                                         

-- Parameter includes NULL
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GeomFromText(null, 4316))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
             
```
