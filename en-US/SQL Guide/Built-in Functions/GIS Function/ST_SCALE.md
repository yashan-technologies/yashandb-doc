```ebnf+diagram
st_scale::= ST_SCALE "(" geometry "," factor_x"," factor_y ")"
```

```ebnf+diagram
st_scale::= ST_SCALE "(" geometry "," factor_x "," factor_y "," factor_z ")"
```

ST_SCALE function is used to shrink or enlarge 2D or 3D spatial objects. It scales the specified spatial object (such as points, lines, surfaces, polygons, etc.) along the X, Y, and Z axes by specified ratios, maintaining the object's starting position.

**geometry**

[Generic Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

If any input parameters are NULL, the function returns NULL.

**factor_x/factor_y/factor_z**

Set the scaling factor for each coordinate axis. The input data type must be DOUBLE or other data types that can be implicitly converted to DOUBLE.

- If any input parameters are NULL, the function returns NULL.
- When the input parameters exceed the precision range of the DOUBLE data type (such as nan, inf, and -inf), the function calculation results for the corresponding coordinate axis will not affect the calculation results of other coordinate axes.


***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_SCALE(ST_GeomFromText('POINT(2 2 1)'), 2, 3,1)) scale FROM dual;

SCALE
----------------------------------------------------------------
POINT Z (4.000000000000000 6.000000000000000 1.000000000000000)

SELECT ST_AsText(ST_SCALE(ST_GeomFromText('POINT(2 2)'), 2, 3)) scale FROM dual;

SCALE
----------------------------------------------------------------
POINT (4.000000000000000 6.000000000000000)

SELECT st_astext(st_scale(st_geomfromtext('point(1 2)', 4326), CAST('-inf' AS FLOAT), 2)) scale FROM dual;

SCALE
---------------------------------------------------------------- 
POINT (-inf 4.000000000000000)                                  

SELECT st_astext(st_scale(NULL, 1, 2)) FROM dual;

ST_ASTEXT(ST_SCALE(NULL,1,2))                                
---------------------------------------------------------------- 
                                                                
```
