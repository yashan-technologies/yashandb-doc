```ebnf+diagram
st_perimeter::= ST_PERIMETER "(" geometry ")"
```

The ST_PERIMETER function is used to calculate the perimeter of a geometry, or in other words, it calculates the perimeter of an area. For geometric shapes that cannot form an area, it returns 0.

**geometry**

[Generic Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* If geometry is of type POLYGON, MULTIPOLYGON, or GEOMETRY COLLECTION, it will return the corresponding perimeter; other geometry types will return 0.
* The Spatial Reference System Identifier (SRID) of geometry must be defined in the spatial_ref_sys system table or be 0; otherwise, an error will be raised.
* If the SRID corresponding to geometry in spatial_ref_sys has an srs_type of GEOGRAPHY2D or GEOGRAPHY3D, it will switch to geodetic coordinate algorithm for calculation; otherwise, it will use projected coordinate algorithm.

This function follows these rules:

* If any input parameter is NULL, the function returns NULL.
* This function only calculates 2D results; if the input is three-dimensional, it will ignore the Z coordinate for the calculation.
* For input latitude and longitude coordinates, if the input values are not within valid latitude and longitude ranges, they will be converted to valid latitude and longitude for calculation.

***Example*** for Heap tables

```sql
SELECT ST_Perimeter(ST_GeomFromText('POLYGON ((-71.17 42.39,-72.17 43.39,-72.17 44.39,-71.17 42.39))', 4326)) res FROM dual;

RES
----------- 
4.855E+005

SELECT ST_Perimeter(ST_GeomFromText('POLYGON((0 0, 10 0, 10 10, 0 10, 0 0))', 3385)) res FROM dual;

RES
----------- 
4.0E+001
```
