```ebnf
st_issimple = ST_ISSIMPLE "(" geometry ")".
```

The ST_ISSIMPLE function returns whether the input geometry is simple. If the geometry meets the definition of simple, it returns TRUE; otherwise, it returns FALSE.

The simple definition of ST_GEOMETRY data follows the OGC SFS specifications and is determined by the following rules:

- POINT: All are simple.
- MULTIPOINT: If all points are unique, the data is simple; otherwise, it is not simple.
- LINESTRING: If all points except for the endpoints are unique, the data is simple; otherwise, it is not simple.
- MULTILINESTRING: If all elements are simple and the unique intersection between any two elements occurs on the boundaries of those two elements, the data is simple; otherwise, it is not simple.
- POLYGON: Composed of simple and closed LINESTRINGs, and if the POLYGON data is valid, then the data is simple; otherwise, it is not simple. The definition of validity can refer to the description of the [ST_ISVALID](ST_ISVALID) function.
- MULTIPOLYGON: If all elements are simple, the data is simple; otherwise, it is not simple.
- GEOMETRYCOLLECTION: If all elements are simple, the data is simple; otherwise, it is not simple.
- The data is simple if all are EMPTY.

**geometry**

[Generic Expression](../../General SQL Syntax/expr), its value must be a valid ST_GEOMETRY data type.

When the input parameter is NULL, the function returns NULL, treating an empty string as NULL.

***Example*** for Heap tables

```sql
SELECT ST_IsSimple(ST_GeomFromText('POLYGON((1 2, 3 4, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
false

SELECT ST_IsSimple(ST_GeomFromText('POLYGON((1 2, 3 3, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
true                
```
