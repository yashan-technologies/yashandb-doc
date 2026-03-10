```ebnf+diagram
st_distance::= ST_DISTANCE "(" geometry1 "," geometry2 ")"
```

The ST_DISTANCE function returns the distance data corresponding to the input geometry1 and geometry2.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be equal, otherwise an error will be reported.
* The spatial reference system identifier (SRID) of geometry1 and geometry2 must be defined in the spatial_ref_sys system table or be 0, otherwise an error will be reported.
* If the srs_type corresponding to the SRID of geometry1 and geometry2 in spatial_ref_sys is GEOGRAPHY2D or GEOGRAPHY3D, the calculation will switch to great-circle distance algorithm; otherwise, the projected coordinate algorithm will be used.

When NULL is present in the input parameters, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT ST_Distance(ST_GeomFromText('linestring(-72.1523 42.6343, -72.4524 42.2872)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.1235 42.3521)',4326)) res FROM dual;

RES 
------------------------- 
                        0

SELECT ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)) res FROM dual;

RES 
------------------------- 
  7.3801729438882347E+001

SELECT ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45))',4326)) res FROM dual;

RES 
------------------------- 
                        0

SELECT ST_Distance(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.4524 42.2872)',4326)) res FROM dual;

RES 
------------------------- 
  2.6136292567874534E+004

SELECT ST_Distance(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4490), ST_GeomFromText('linestring(-72.4524 42.4526, -72.4524 42.2872)',4490)) res FROM dual;

RES 
------------------------- 
   2.613629256806742E+004

SELECT ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45))',4326)) res FROM dual;

RES 
------------------------- 
                        0

SELECT ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('linestring(-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45)',4326)) res FROM dual;

RES 
------------------------- 
  1.2380207674721363E+002

SELECT ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('multipoint(-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45)',4326)) res FROM dual;

RES 
------------------------- 
  3.7091260963845019E+002
```
