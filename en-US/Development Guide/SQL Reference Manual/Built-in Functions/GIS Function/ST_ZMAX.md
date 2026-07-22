```ebnf
st_zmax = ST_ZMAX "(" expr ")".
```

The ST_ZMAX function returns the Z maxima of the input 2D or 3D geometry object.

**expr**

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
select ST_ZMax(st_geomfromtext('MULTIPOINT Z(1 3  6  , 2   4 8)')) as res from dual ;

        RES 
----------- 
   8.0E+000
```
