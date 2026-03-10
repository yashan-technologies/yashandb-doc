```ebnf+diagram
st_srid::= ST_SRID "(" geometry ")"
```

The ST_SRID function is used to query the spatial reference system identifier (SRID) of the input geometry.

This function returns an integer type SRID based on the input geometry.

**geometry**

Geometry is valid ST_GEOMETRY type data.

If the input parameters contain NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_SRID(ST_GeomFromText('POINT(-58.2687 29.149)',1356)) res FROM DUAL;

         RES 
------------ 
        1356

-- ST_GEOMFROMWKB function returns an ST_GEOMETRY data based on the given WKB and SRID
SELECT ST_SRID(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040',4322)) res FROM DUAL;

         RES 
------------ 
        4322

-- Parameter contains NULL
SELECT ST_SRID(ST_GeomFromText(null,1356)) res FROM DUAL;

         RES 
------------ 
            
```
