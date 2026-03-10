```ebnf+diagram
st_buffer::= ST_BUFFER "(" geometry "," width [ "," style ] ")"
```

The functionality of the ST_BUFFER function is to return an ST_GEOMETRY type data that covers all points from the input geometry to the given distance width. The calculated result is always a valid POLYGON data.

If any input parameters are NULL, the function will return NULL.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* Only 2D results are supported. If the geometry coordinates contain a Z-axis, the Z-coordinate will be ignored for the calculation, and the result will still be a 2D ST_GEOMETRY data.
* If the input is an EMPTY ST_GEOMETRY data, it will return POLYGON EMPTY (if a GEOMETRYCOLLECTION has only part of it as EMPTY, the calculation result may not necessarily be POLYGON EMPTY).
* The coordinates of the input geometry must not contain illegal numbers (such as inf, nan), otherwise, an error will be reported.

**width**

Width is used to specify the distance, and its value is of DOUBLE type, following these rules:

* Supports data types that can be implicitly converted to DOUBLE.
* If the input width is a negative value, it will shrink the geometry, and in extreme cases, the resulting POLYGON data may shrink to 0, thus returning POLYGON EMPTY; for POINT and LINESTRING types, if width is a negative value, it will always return POLYGON EMPTY.
* The unit of width is the unit of the spatial reference system of the input geometry.

**style**

Style is used to control the precision and style of the result, and its value is of VARCHAR type. This parameter is optional; if omitted, default values will be applied to precision and style.

* Supports data types that can be implicitly converted to VARCHAR.
* Style has five parameters that can be set, specified using the `key=value` format, with different key-value pairs separated by spaces, the order of key-value pairs does not matter, and case is ignored. Some keys have aliases, and the specific rules are as follows:
  * `quad_segs=#` : Indicates how many segments (line segments) the quarter-circle has, with a default value of 8. If less than 0, it takes 0, the maximum value is 262144, and if exceeded, it will be calculated as the maximum value. If a decimal is input, it will be truncated to an integer; if invalid data is input, it converts to 0; if the result length exceeds 65534, an error will be reported.
  * `endcap=round|flat(butt)|square` : Endpoint style, with a default value of round.
  * `mitre_limit(miter_limit)=#.#` : Used to limit the bevel ratio, which only affects the `join=mitre(miter)` situation, with a default value of 5.0.
  * `join=round|mitre(miter)|bevel`: Join style, with a default value of round.
  * `side=both|left|right` : Left and right indicate the formation of a unilateral shape, only affecting LINESTRING types, not affecting POINT or POLYGON types (which still form closed ST_GEOMETRY data). Both will form a closed ST_GEOMETRY data, with a default value of both.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POLYGON((50 50, 150 150, 150 50, 50 50))'), -2, 'quad_segs=1'), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON ((55 52, 148 145, 148 52, 55 52))                       

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POLYGON((50 50, 150 150, 150 50, 50 50))'), 1, 'quad_segs=1'), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON ((50 49, 49 50, 49 51, 149 151, 150 151, 151 150, 151 50, 150 49, 50 49))
    
SELECT ST_AsText(ST_Buffer(ST_GeomFromText('LINESTRING(1 3 5, 2 4 6, 1 3 5)'), 1, 'quad_segs=2 join=bevel'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 3, 0 4, 1 5, 3 3, 2 2, 1 2, 0 2, 0 3))              

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('LINESTRING(1 3 5, 2 4 6, 1 3 5)'), 1, 'quad_segs=2'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 3, 0 4, 1 5, 2 5, 3 5, 3 4, 3 3, 2 2, 1 2, 0 2, 0 3))

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POINT(100 90 100)'), 1, 'quad_segs=1'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((101 90, 100 89, 99 90, 100 91, 101 90))               
```
