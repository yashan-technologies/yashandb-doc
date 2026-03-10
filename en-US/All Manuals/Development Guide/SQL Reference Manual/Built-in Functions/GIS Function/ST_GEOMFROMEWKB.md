```ebnf+diagram
st_geomfromewkb::= ST_GEOMFROMEWKB "(" ewkb ")"
```

The ST_GEOMFROMEWKB function returns a ST_GEOMETRY type data based on the given ewkb (Extended Well-Known Binary).

**ewkb**

The data type of ewkb is BLOB and follows these rules:

*   Supports types that can be implicitly converted to BLOB.
*   ewkb needs to be a valid Extended Well-Known Binary; otherwise, an error is reported.
*   If the input ewkb has leading characters that form a valid ST_GEOMETRY type data and there are other valid characters after, only the leading valid ST_GEOMETRY type data will be generated.

When the input ewkb is NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_GeomFromEwkb('0101000020E6100000000000000000F03F0000000000000040')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1.000000000000000 2.000000000000000)                     

SELECT ST_Srid(ST_GeomFromEwkb('0101000020E6100000000000000000F03F0000000000000040')) res FROM DUAL;

         RES 
------------ 
        4326                                   
```
