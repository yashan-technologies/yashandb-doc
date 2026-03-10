```ebnf+diagram
st_length::= ST_LENGTH "(" geometry ")"
```

The ST_LENGTH function returns the corresponding length data based on the input geometry.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* If the geometry is of type LINESTRING, MULTILINESTRING, or GEOMETRY COLLECTION, it will return the corresponding length; other geometry types will return 0.
* The spatial reference identifier number (SRID) of the geometry must be defined in the spatial_ref_sys system table or be 0; otherwise, an error will occur.
* If the spatial reference identifier number (SRID) of the geometry corresponds to srs_type GEOGRAPHY2D or GEOGRAPHY3D in spatial_ref_sys, it will switch to geodetic coordinate algorithm for calculation; otherwise, it will use the projected coordinate algorithm.

If the input parameter is NULL, the function will return NULL.

***Example*** for Heap tables

```sql
SELECT ST_Length(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4326)) res FROM dual;

        RES
-----------
 3.144E+004

SELECT ST_Length(ST_GeomFromText('linestring(5000 6789, 12345 5789)', 3385)) res FROM dual;

        RES
-----------
 7.413E+003
```
