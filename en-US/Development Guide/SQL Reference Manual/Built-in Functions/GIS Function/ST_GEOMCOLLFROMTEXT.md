```ebnf
st_geomcollfromtext = ST_GEOMCOLLFROMTEXT "(" wkt [ "," srid ] ")".
```

The ST_GEOMCOLLFROMTEXT function returns an ST_GEOMETRY type data based on the given wkt (Well-Known Text) and srid. If the provided WKT is not a GEOMETRYCOLLECTION, it returns NULL.

The specifications for the input parameters wkt and srid are the same as the [ST_GEOMFROMTEXT](./ST_GEOMFROMTEXT ST_GEOMETRYFROMTEXT) function.

When any input parameter is NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_GeomCollFromText('GEOMETRYCOLLECTION EMPTY')) res FROM DUAL;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY                                        

SELECT ST_AsText(ST_GeomCollFromText('GEOMETRYCOLLECTION(POINT(1 2), LINESTRING(1 3, 4 1))'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION (POINT (1 2), LINESTRING (1 3, 4 1))         

SELECT ST_AsText(ST_GeomCollFromText('LINESTRING(1 3, 4 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
```
