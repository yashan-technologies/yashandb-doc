```ebnf+diagram
st_asbinary::= ST_ASBINARY "(" geometry ["," byteorder ]")"
```
```ebnf+diagram
st_asbinary::= ST_ASBINARY "(" box2d ["," byteorder ]")"
```

The ST_ASBINARY function returns the WKB (Well-Known Binary) representation of the geometry (the geometry corresponding to box2d) based on the input of geometry (box2d) and byteorder.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**box2d**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid BOX2D type data. The output rules for this parameter are as follows:

*   If box2d is a point, the corresponding POINT's WKB is output.
*   If box2d is a line, the corresponding LINESTRING's WKB is output.
*   If box2d is a rectangle, the corresponding POLYGON's WKB is output.
*   The SRID of the output geometry is 0.

**byteorder**

byteorder indicates the byte order of the output result, its value is of type VARCHAR and follows these rules:

*   Supports data types that can be implicitly converted to VARCHAR.
*   "NDR" means little-endian, "XDR" means big-endian, case insensitive, other input strings will result in an error.
*   This parameter can be omitted; if omitted, the default is to use the server computer's byte order for encoding.

If any input parameter is NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
-- The output WKB does not contain SRID
SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

-- Little-endian and big-endian 
SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)'), 'NDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsBinary(ST_GeomFromText('POINT(1 2)'), 'XDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
00000000013FF00000000000004000000000000000
    
-- box2d is a point
SELECT ST_AsBinary(BOX2D(1, 1, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
0101000000000000000000F03F0000000000000040

-- box2d is a line
SELECT ST_AsBinary(BOX2D(1, 3, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
010200000002000000000000000000F03F000000000000004000000000000008400000000000000040

-- box2d is a rectangle    
SELECT ST_AsBinary(BOX2D(1, 3, 2, 3)) res FROM DUAL;

RES
----------------------------------------------------------------
01030000000100000005000000000000000000F03F0000000000000040000000000000F03F00000000000008400000000000000840000000000000084000000000000008400000000000000040000000000000F03F0000000000000040
```
