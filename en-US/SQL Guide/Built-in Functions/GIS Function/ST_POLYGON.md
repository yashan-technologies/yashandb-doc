```ebnf+diagram
st_polygon::= ST_POLYGON "(" geometry "," srid ")"
```

The ST_POLYGON function returns POLYGON data composed of geometry in the corresponding srid based on the input geometry and srid.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, and follows these rules:

* The geometry must be of LINESTRING type.
* The number of POINTs in the geometry must be greater than or equal to 4.
* The first and last 2 POINTs of the geometry must be the same.

**srid**

The data type of srid is INT, indicating the spatial reference system in the output result, and follows these rules:

*   Types that can be implicitly converted to INT are supported; for example, inputting a decimal will be rounded.
*   If the input is a negative number, it will be calculated based on the spatial reference system identifier (SRID) of the geometry itself.

When any input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
--ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_Polygon(ST_GeomFromText('LINESTRING(0 0, 1 1, 2 3, 0 0 )'), 0), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 0, 1 1, 2 3, 0 0))    

SELECT ST_AsText(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)'), 4326), 0) res FROM DUAL;

RES                                           
---------------------------------------------------------------- 
POLYGON Z ((0 0 0, 1 1 1, 2 3 4, 0 0 0))

--When SRID is a negative number, it is calculated according to the SRID of the geometry itself
SELECT ST_SRID(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)',5111), -4326)) res FROM DUAL;

         RES 
------------ 
        5111  

SELECT ST_SRID(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)'), 4326)) res FROM DUAL;

         RES 
------------ 
        4326
```
