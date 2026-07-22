```ebnf
st_linefromtext = ST_LINEFROMTEXT "(" wkt [ "," srid ] ")".
```

The ST_LINEFROMTEXT function returns an ST_GEOMETRY type data based on the given wkt (Well-Known Text) and srid. If the input WKT is not a LINESTRING, it returns NULL.

The specifications for the input parameters wkt and srid are the same as those of the [ST_GEOMFROMTEXT](./ST_GEOMFROMTEXT ST_GEOMETRYFROMTEXT) function.

When the input parameters contain NULL, the function returns NULL. An empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_LineFromText('LINESTRING EMPTY')) res FROM DUAL;

RES
---------------------------------------------------------------- 
LINESTRING EMPTY                                                

SELECT ST_AsText(ST_LineFromText('LINESTRING(1 1, 3 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
LINESTRING (1 1, 3 1)                                           

SELECT ST_AsText(ST_LineFromText('POINT(3 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
```
