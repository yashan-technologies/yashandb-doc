```ebnf
st_translate = ST_TRANSLATE "(" geometry "," delta_x"," delta_y ")".
```

```ebnf
st_translate = ST_TRANSLATE "(" geometry "," delta_x "," delta_y "," delta_z ")".
```

ST_TRANSLATE function is used to translate 2D or 3D spatial objects. It moves the specified spatial object (such as points, lines, surfaces, polygons, etc.) along the X, Y, and Z axes by specified distances, maintaining the object's shape, size, and orientation while only changing its position in space.

**geometry**

[Generic Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

If any input parameters are NULL, the function returns NULL.

**delta_x/delta_y/delta_z**

Specify the translation distance for each coordinate axis. The input data type must be DOUBLE or other data types that can be implicitly converted to DOUBLE.

- If any input parameters are NULL, the function returns NULL.
- When the input parameters exceed the precision range of the DOUBLE data type (such as nan, inf, and -inf), the function calculation results for the corresponding coordinate axis will not affect the calculation results of other coordinate axes.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_TRANSLATE(ST_GeomFromText('POINT(1 1)'), 5, 3)) res FROM dual;

RES
----------------------------------------------------------------
POINT (6.000000000000000 4.000000000000000)

SELECT ST_AsText(ST_TRANSLATE(ST_GeomFromText('POINT(1 1 1)'), 5, 3,1)) res FROM dual;

RES
----------------------------------------------------------------
POINT Z (6.000000000000000 4.000000000000000 2.000000000000000)

select st_astext(st_translate(NULL, 1, 2)) from dual;

ST_ASTEXT(ST_TRANSLATE(NULL,1,2))                                
---------------------------------------------------------------- 
                                                                
select st_astext(st_translate(st_geomfromtext('point(1 2 3)', 4326), 2, 2, cast('inf' as double))) res from dual;

RES
---------------------------------------------------------------- 
POINT Z (3.000000000000000 4.000000000000000 inf)     
```
