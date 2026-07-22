```ebnf
st_setsrid = ST_SETSRID "(" geometry "," srid ")".
```

The ST_SETSRID function is used to set the spatial reference identifier (SRID) of the input geometry to the specified srid.

This function returns the geometry with the SRID set according to the input geometry.

**geometry**

[Generic Expression](../../General SQL Syntax/expr), the value must be valid ST_GEOMETRY type data.

**srid**

srid represents the SRID to be set, and its value is of INT type.

* If the input srid is negative, it is treated as 0.

* Types that can be implicitly converted to INT are supported. If a decimal is input, it will be rounded.

If any input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function will return an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_SetSrid(ST_GeomFromText('POINT(-58.2687 29.149)',1356), 4326)) res FROM DUAL;

RES
----------------------------------------------------------------
POINT (-58.268700000000003 29.149000000000001)

-- The ST_GEOMFROMWKB function will return an ST_GEOMETRY data based on the given WKB and SRID
SELECT ST_SRID(ST_SetSrid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040',4322), 4326)) res FROM DUAL;

        RES
------------ 
        4326

-- Parameter includes NULL
SELECT ST_SRID(ST_SetSrid(ST_GeomFromText(null,1356), 4326)) res from DUAL;

         RES 
------------ 
            
```
