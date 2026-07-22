```ebnf
st_isvalid = ST_ISVALID "(" geometry ")".
```

The ST_ISVALID function returns whether the input geometry is valid. If the geometry is valid, it returns TRUE; otherwise, it returns FALSE.

The validity of ST_GEOMETRY type data must comply with the OGC SFS specification and is determined according to the following rules:

- POINT: All are valid.
- MULTIPOINT: All are valid.
- LINESTRING: All are valid.
- MULTILINESTRING: All are valid.
- POLYGON: Valid POLYGON type data must adhere to the following constraints; otherwise, it is considered invalid:
  - All rings must be closed.
  - Inner rings must be within the outer ring.
  - Rings must not intersect themselves.
  - Rings must not touch each other, unless they are tangent at a point.
- MULTIPOLYGON: The data is valid if all elements are valid and do not intersect internally; otherwise, it is not valid.
- GEOMETRYCOLLECTION: The data is valid if all elements are valid; otherwise, it is not valid.
- Data is valid if all entries are EMPTY.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

When input parameters contain NULL, the function returns NULL, and an empty string is treated as NULL.

Only 2D results are computed; if Z coordinates are present in the input parameters, the function will directly ignore the Z coordinates for computation.

***Example*** for Heap tables

```sql
SELECT ST_IsValid(ST_GeomFromText('LINESTRING(0 0, 1 1)')) res FROM DUAL;

RES
-------------------- 
true

SELECT ST_IsValid(ST_GeomFromText('POLYGON((0 0, 1 1, 1 2, 1 1, 0 0))')) res FROM DUAL;

RES
-------------------- 
false               
```
