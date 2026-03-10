```ebnf+diagram
st_astext::= ST_ASTEXT "(" geometry [ "," maxdecimaldigits ] ")"
```
```ebnf+diagram
st_astext::= ST_ASTEXT "(" box2d [ "," maxdecimaldigits ] ")"
```

The ST_ASTEXT function returns the WKT (Well-Known Text) representation of the geometry corresponding to the input geometry (box2d) and maxdecimaldigits.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**box2d**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid BOX2D type data. The output rules for this parameter are as follows:

* If box2d is a point, the corresponding POINT's WKT will be output.
* If box2d is a line, the corresponding LINESTRING's WKT will be output.
* If box2d is a rectangle, the corresponding POLYGON's WKT will be output.
* The SRID of the output geometry is 0.

**maxdecimaldigits**

maxdecimaldigits indicates the number of decimal places in the output result. If it is insufficient, zeros will be added. Its value is of INT type and follows these rules:

* Supports types that can be implicitly converted to INT. If the input is a decimal, it will be rounded.
* This parameter can be omitted; when omitted, the default value is 15.
* If a negative value is input, it will output as 0.
* If the input value exceeds 16, it will be processed as 16, but the 16th decimal place may not be valid.
* The result will not be output in scientific notation.

If any input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function will return ST_GEOMETRY data based on the given WKT and SRID
-- Decimal places
SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 1) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1.0 2.0)                                                 

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 100) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1.0000000000000000 2.0000000000000000)                   

SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), -10) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
POINT (1 2)                                                     
                                                   
-- Mixed dimensions
SELECT ST_AsText(ST_GeomFromText('MULTIPOINT(1 2 3, 1 1)', 4326), 0) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
MULTIPOINT Z (1 2 3, 1 1 0)                                     

-- Supports inputs of inf and nan
SELECT ST_AsText(St_GeomFromText('MULTIPOINT(inf inf,nan nan)')) res FROM DUAL;

RES                                               
---------------------------------------------------------------- 
MULTIPOINT (inf inf, EMPTY)
    
-- box2d is a point
SELECT ST_AsText(BOX2D(1, 1, 2, 2), 0) res FROM DUAL;

RES
----------------------------------------------------------------
POINT (1 2)
    
-- box2d is a line   
SELECT ST_AsText(BOX2D(1, 3, 2, 2), 0) res FROM DUAL;

RES
----------------------------------------------------------------
LINESTRING (1 2, 3 2)

-- box2d is a rectangle    
SELECT ST_AsText(BOX2D(1, 3, 2, 3), 0) res FROM DUAL;

RES
----------------------------------------------------------------
POLYGON ((1 2, 1 3, 3 3, 3 2, 1 2))
```
