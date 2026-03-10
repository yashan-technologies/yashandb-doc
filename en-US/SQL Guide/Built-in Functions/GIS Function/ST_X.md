```ebnf+diagram
st_x::= ST_X "(" geometry ")"
```

The ST_X function returns the x-axis coordinate of the point based on the input geometry.

**geometry**

[General expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

When the input parameter contains NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_X(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES
----------- 
1.0E+000
```
