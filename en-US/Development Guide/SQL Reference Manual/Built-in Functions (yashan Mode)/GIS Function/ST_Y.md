```ebnf+diagram
st_y::= ST_Y "(" geometry ")"
```

The ST_Y function returns the y-coordinate of a point based on the input geometry.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

When a NULL parameter is present, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_Y(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES
----------- 
2.0E+000
```
