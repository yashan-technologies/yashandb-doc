```ebnf
st_ashexewkb = ST_ASHEXEWKB "(" geometry "," byteorder ")".
```

```ebnf
st_ashexewkb = ST_ASHEXEWKB "(" box2d "," byteorder ")".
```

The ST_ASHEXEWKB function returns the hexewkb (hexadecimal representation of the Extended Well-Known Binary) for the provided geometry (corresponding to box2d) and byteorder.

**geometry**

[General Expression](../../General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

**box2d**

[General Expression](../../General SQL Syntax/expr), whose value must be valid BOX2D type data. The output rules for this parameter are as follows:

*   If box2d is a point, it outputs the corresponding HEXEWKB for POINT.
*   If box2d is a line, it outputs the corresponding HEXEWKB for LINESTRING.
*   If box2d is a rectangle, it outputs the corresponding HEXEWKB for POLYGON.
*   The SRID of the output geometry is 0.

**byteorder**

The data type of byteorder is VARCHAR, which indicates the byte order of the output result.

*   Supports data types that can be implicitly converted to VARCHAR.
*   "NDR" indicates little-endian, "XDR" indicates big-endian, case insensitive; inputting other strings will result in an error.
*   This parameter can be omitted. When omitted, the server's computer byte order is used by default.

When any input parameter is NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
-- Output wkb includes srid
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
0101000020E6100000000000000000F03F0000000000000040              

-- Little-endian and Big-endian
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), 'NDR') res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
0101000000000000000000F03F0000000000000040                      

SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), 'XDR') res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
00000000013FF00000000000004000000000000000      

-- Parameters include NULL
SELECT ST_AsHexEwkb(NULL, 'XDR') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
SELECT ST_AsHexEwkb(ST_GeomFromText('POINT(1 2)'), NULL) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
 
```
