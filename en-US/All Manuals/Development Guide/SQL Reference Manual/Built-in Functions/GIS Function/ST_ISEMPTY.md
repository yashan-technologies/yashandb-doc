```ebnf+diagram
st_isempty::= ST_ISEMPTY "(" geometry ")"
```

The ST_ISEMPTY function returns whether the input geometry is empty, i.e., EMPTY. If the geometry is entirely EMPTY, it returns TRUE; otherwise, it returns FALSE.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

When the input parameter is NULL, the function returns NULL, and an empty string is treated as NULL.

***Example*** for Heap tables

```sql
SELECT ST_IsEmpty(ST_GeomFromText('POLYGON EMPTY')) res FROM DUAL;

RES
-------------------- 
true

SELECT ST_IsEmpty(ST_GeomFromText('POLYGON((1 2, 3 4, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
false               
```
