```ebnf+diagram
st_xmin::= ST_XMIN "(" geometry ")"
```

The ST_XMIN function returns the X minima of the input 2D or 3D geometry object.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY or BOX2D type data.

Currently supported input subtypes of ST_GEOMETRY include:

- POINT
- LINESTRING
- POLYGON
- MULTIPOINT
- MULTILINESTRING
- MULTIPOLYGON
- GEOMETRYCOLLECTION

If NULL is input, an error message will be returned.

***Example*** for Heap tables

```sql
SELECT ST_XMin(st_geomfromtext('MULTIPOINT Z(1 3 6 , 2 4 8)')) AS res FROM dual ;

        RES 
----------- 
   1.0E+000
```
