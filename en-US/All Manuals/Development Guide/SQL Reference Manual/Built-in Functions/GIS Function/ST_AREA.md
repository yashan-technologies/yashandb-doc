```ebnf+diagram
st_area::= ST_AREA "(" geometry ")"
```

The ST_AREA function is used to calculate the area of a geometry, or in other words, it calculates the area of a region. For geometric shapes that do not form a region, it returns 0.

**geometry**

[General Expression](../../General SQL Syntax/expr), the value must be valid ST_GEOMETRY type data and follows these rules:

* If the geometry is of type POLYGON, MULTIPOLYGON, or GEOMETRY COLLECTION, it will return the corresponding area; other geometry types will return 0.
* The spatial reference system identifier (SRID) of the geometry must be defined in the spatial_ref_sys system table, or it must be 0; otherwise, an error will be raised.
* If the SRID of the geometry corresponds to srs_type GEOGRAPHY2D or GEOGRAPHY3D in spatial_ref_sys, it will switch to geodetic coordinate calculations; otherwise, it will use projected coordinate calculations.

This function adheres to the following rules:

* If any input parameter is NULL, the function returns NULL.
* This function will only calculate 2D results; if the input is three-dimensional, the Z coordinate will be ignored in the calculation.
* For input latitude and longitude coordinates, if the input values are not within valid latitude and longitude ranges, they will be converted to valid latitude and longitude for calculation.

***Example*** for Heap tables

```sql
SELECT ST_Area(ST_GeomFromText('POLYGON ((-71.17 42.39,-72.17 43.39,-72.17 44.39,-71.17 42.39))', 4326)) res FROM dual;

RES
----------- 
4.575E+009

SELECT ST_Area(ST_GeomFromText('POLYGON((0 0, 10 0, 10 10, 0 10, 0 0))', 3385)) res FROM dual;

RES
----------- 
1.0E+002
```
