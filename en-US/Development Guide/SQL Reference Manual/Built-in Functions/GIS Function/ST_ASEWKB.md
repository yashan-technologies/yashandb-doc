```ebnf
st_asewkb = ST_ASEWKB "(" geometry ["," byteorder ]")".
```
```ebnf
st_asewkb = ST_ASEWKB "(" box2d ["," byteorder ]")".
```

The ST_ASEWKB function returns the EWKB (Extended Well-Known Binary) representation of the geometry (box2d corresponding geometry) based on the input geometry (box2d) and byteorder.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**box2d**

[General Expression](../../General SQL Syntax/expr), its value must be valid BOX2D type data. The output rules for this parameter are as follows:

*   If the box2d is a point, the corresponding POINT's EWKB is returned.
*   If the box2d is a line, the corresponding LINESTRING's EWKB is returned.
*   If the box2d is a rectangle, the corresponding POLYGON's EWKB is returned.
*   The SRID of the output geometry is 0.

**byteorder**

The byteorder indicates the byte order of the output result, and its value is of VARCHAR type, following these rules:

*   Data types that can be implicitly converted to VARCHAR are supported.
*   "NDR" indicates little-endian, "XDR" indicates big-endian, case insensitive; other strings return an error.
*   This parameter can be omitted; when omitted, the server computer's byte order is used by default.

If any input parameter is NULL, the function returns NULL, and empty strings are treated as NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
-- The output EWKB does not include SRID
SELECT ST_AsEwkb(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                                       

SELECT ST_AsEwkb(ST_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

RES                                              
---------------------------------------------------------------- 
0101000020E6100000000000000000F03F0000000000000040              

-- Little-endian and big-endian
SELECT ST_AsEwkb(ST_GeomFromText('POINT(1 2)'), 'NDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsEwkb(ST_GeomFromText('POINT(1 2)'), 'XDR') res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
00000000013FF00000000000004000000000000000                      

-- box2d as point
SELECT ST_AsEwkb(BOX2D(1, 1, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
0101000000000000000000F03F0000000000000040

-- box2d as line
SELECT ST_AsEwkb(BOX2D(1, 3, 2, 2)) res FROM DUAL;

RES
----------------------------------------------------------------
010200000002000000000000000000F03F000000000000004000000000000008400000000000000040

-- box2d as rectangle  
SELECT ST_AsEwkb(BOX2D(1, 3, 2, 3)) res FROM DUAL;

RES
----------------------------------------------------------------
01030000000100000005000000000000000000F03F0000000000000040000000000000F03F00000000000008400000000000000840000000000000084000000000000008400000000000000040000000000000F03F0000000000000040
```
