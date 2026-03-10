```ebnf+diagram
st_transform::= ST_TRANSFORM "(" geometry "," srid ")"
```

```ebnf+diagram
st_transform::= ST_TRANSFORM "(" geometry "," from_proj "," srid ")"
```

The ST_TRANSFORM function returns a new geometry with coordinates transformed from the original spatial reference system to the spatial reference system specified by srid based on the input geometry and srid.

**geometry**

[Generic Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

*   A srid must be present, and it must be defined in the system table spatial_ref_sys; otherwise, an error will be raised.

**from_proj**

String source spatial reference.

**srid**

The data type of srid is INT, representing the spatial reference system in the output result, and follows these rules:

*   Types that can be implicitly converted to INT are supported, for example, decimal inputs will be rounded.
*   Must be defined in the system table spatial_ref_sys; otherwise, an error will be raised.

If any input parameters are NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_Transform(ST_GeomFromText('polygon((73.62 16.7, 74.15 16.45, 73.77 16.32, 73.62 16.7))', 4326), 4491),2) res FROM dual;

RES                                             
---------------------------------------------------------------- 
POLYGON ((13352805.80 1847616.32, 13409224.37 1819631.38, 13368550.72 1805451.07, 13352805.80 1847616.32))

SELECT ST_AsText(ST_Transform(ST_GeomFromText('linestring(73.62 16.7, 74.15 16.45)', 4326), 4491),2) res FROM dual;

RES                                             
---------------------------------------------------------------- 
LINESTRING (13352805.80 1847616.32, 13409224.37 1819631.38)
```
