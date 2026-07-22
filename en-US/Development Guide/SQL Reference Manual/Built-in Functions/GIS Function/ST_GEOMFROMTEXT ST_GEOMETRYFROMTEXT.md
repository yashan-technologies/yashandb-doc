```ebnf
st_geomfromtext = ST_GEOMFROMTEXT "(" wkt [ "," srid ] ")".
```

The ST_GEOMFROMTEXT (alias: ST_GEOMETRYFROMTEXT) function returns an ST_GEOMETRY type data based on the given wkt (Well-Known Text) and srid.

**wkt**

The data type of wkt is CLOB, following these rules:

*   The wkt must be a valid Well-Known Text, otherwise an error will be raised.
*   The highest supported dimension for coordinates is three-dimensional. If the input is four-dimensional coordinates, or coordinates containing 'M', the fourth coordinate axis will be ignored, generating three-dimensional coordinates.
*   If the input coordinates include both two-dimensional points and three-dimensional points, the two-dimensional points will be elevated to three-dimensional, with the Z-axis value set to zero.
*   The input supports 'inf' and 'nan', and if a coordinate consists entirely of 'nan', it will be stored as EMPTY.
*   The input supports empty ST_GEOMETRY data (e.g., POINT EMPTY).
*   Point coordinates cannot be used together with EMPTY; for example, MULTIPOINT(1 1, EMPTY) will raise an error.
*   If the preceding characters of the input wkt already form a valid ST_GEOMETRY data, and there are additional characters following, only the preceding valid ST_GEOMETRY data will be generated.
*   It supports data types that can be implicitly converted to CLOB.

**srid**

The data type of srid is INT, representing the spatial reference system of the ST_GEOMETRY type data, following these rules:

*   It supports types that can be implicitly converted to INT. If the input is a decimal, it will be rounded.
*   This parameter can be omitted; if omitted, the default value is 0 (srid=0 indicates that the spatial reference system is not defined).
*   If the input is a negative number, it will be treated as 0.

If the input parameters contain NULL, the function returns NULL; an empty string is treated as NULL.

***Example*** for Heap tables

```sql
--Generate Point
SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)                                                     

--Dimension Mixing
SELECT ST_AsText(ST_GeomFromText('MULTIPOINT(1 2 3, 1 1)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
MULTIPOINT Z (1 2 3, 1 1 0)                                     

--Support for inf and nan input
SELECT ST_AsText(St_GeomFromText('MULTIPOINT(inf inf,nan nan)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
MULTIPOINT (inf inf, EMPTY)                                     

--Srid
SELECT ST_Srid(st_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

         RES 
------------ 
        4326

SELECT ST_Srid(st_GeomFromText('POINT(1 2)')) res FROM DUAL;

         RES 
------------ 
           0

SELECT ST_Srid(st_GeomFromText('POINT(1 2)', -12)) res FROM DUAL;

         RES 
------------ 
           0

--Function with the same name ST_GeometryFromText
SELECT ST_AsText(ST_GeometryFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)
```
