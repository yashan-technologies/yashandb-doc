```ebnf
st_z = ST_Z "(" geometry ")".
```

The ST_Z function is used to return the z-axis coordinate of the input geometry.

This function follows these rules:

- When the input parameter is NULL, the function returns NULL. 
- When the input Geometry type is not Point, an error will be returned. 
- When the coordinates of the input Geometry are only 2-dimensional, NULL will be returned.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

***Example*** for Heap tables

```sql
SELECT ST_Z(ST_GeomFromText('POINT(1 2 3)')) res FROM DUAL;

RES
----------- 
3.0E+000

SELECT ST_Z(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

        RES
-----------

```
