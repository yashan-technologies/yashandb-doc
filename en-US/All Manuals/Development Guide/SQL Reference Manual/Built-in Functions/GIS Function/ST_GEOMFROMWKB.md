```ebnf+diagram
st_geomfromwkb::= ST_GEOMFROMWKB "(" wkb [ "," srid ] ")"
```

The ST_GEOMFROMWKB function returns an ST_GEOMETRY type data based on the given wkb (Well-Known Binary) and srid.

**wkb**

The data type of wkb is BLOB, and it follows these rules:

*   Supports types that can be implicitly converted to BLOB.
*   The wkb must be a valid Well-Known Binary; otherwise, an error will occur.
*   If the input wkb has characters that form a valid ST_GEOMETRY data at the beginning and additional valid characters follow, only the valid ST_GEOMETRY data at the front will be generated.

**srid**

The data type of srid is INT, representing the spatial reference system of the ST_GEOMETRY data, and it follows these descriptions:

*   Supports types that can be implicitly converted to INT; if the input is a decimal, it will be rounded off.
*   This parameter can be omitted, and when omitted, the default value is 0 (srid=0 indicates no spatial reference system is defined).
*   If the input is a negative number, the original SRID will be returned.

When any input parameter is NULL, the function returns NULL; an empty string is treated as NULL.

***Example*** for Heap tables

```sql
--Generate Point
SELECT ST_AsText(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1.000000000000000 2.000000000000000)                     
                                                                          
--Srid
SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040', 4322)) res FROM DUAL;

         RES 
------------ 
        4322

SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040')) res FROM DUAL;

         RES 
------------ 
           0

SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040', -10)) res FROM DUAL;

         RES 
------------ 
           0                                        
```
