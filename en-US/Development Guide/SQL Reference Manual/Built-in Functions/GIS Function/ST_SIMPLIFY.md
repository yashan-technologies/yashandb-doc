```ebnf
st_simplify = ST_SIMPLIFY "(" geometry "," tolerance ")".
```

The functionality of the ST_SIMPLIFY function is to simplify the input geometry using the Douglas-Peucker algorithm.

When any input parameter is NULL, the function returns NULL.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* Only 2D results are computed; if there is a Z coordinate in the input coordinates, the Z coordinate will be ignored in the calculation.
* If the input geometry is of type POINT or MULTIPOINT, it returns the original value directly.
* If the input geometry is of type LINESTRING or MULTILINESTRING, it only returns EMPTY if the input is LINESTRING EMPTY or MULTILINESTRING EMPTY; otherwise, it retains at least two points.
* If the input geometry is of type POLYGON or MULTIPOLYGON, after simplification to a certain extent, it may return POLYGON EMPTY or MULTIPOLYGON EMPTY.
* If the input geometry is of type GEOMETRYCOLLECTION, the internal data changes will follow the same rules as mentioned above.

**tolerance**

Tolerance represents the tolerance value, which is of DOUBLE type. A larger tolerance results in greater simplification, following these rules:

* It supports data types that can be implicitly converted to DOUBLE.
* If a negative number is input, it is converted to the corresponding positive number for calculation.
* If the tolerance value is too large, the input geometry may disappear (become EMPTY).

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_Simplify(ST_GeomFromText('LINESTRING (3 5, 4 6, 2 1, 3 5)'), 100), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (3 5, 3 5)                                           

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), 0.5), 0) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))                             

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), 1), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON EMPTY                                                   

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), NULL), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
```
