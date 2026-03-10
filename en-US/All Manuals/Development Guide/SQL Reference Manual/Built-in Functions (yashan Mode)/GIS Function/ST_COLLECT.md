```ebnf+diagram
st_collect::= ST_COLLECT "(" geometry1 "," geometry2 ")"
```
```ebnf+diagram
st_collect::= ST_COLLECT "(" geomFiled ")"
```

The functionality of the ST_COLLECT function is to aggregate a set of input geometries. Depending on whether the geometries in the set have the same or different types, it generates a GEOMETRYCOLLECTION or MULTI* geometry.

There are two forms of ST_COLLECT. When the parameters are two geometry inputs, this function operates as a regular function. When the parameters are a set of geometry fields, this function operates as an aggregate function.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

The set of input geometries must have the same spatial reference identifier (SRID) and the same spatial dimension; otherwise, an error will be reported.

This function adheres to the following rules:

* When all input parameters are NULL, the function returns NULL.
* When all input geometries are of the same atomic type, it will return a MULTI* geometry; otherwise, it will return a GEOMETRYCOLLECTION.
* This function supports calculations with 3D coordinates.
* This function requires that the input geometries have the same dimension; otherwise, an error will be reported.
* This function requires that the input geometries have the same SRID; otherwise, an error will be reported.
* Geometries cannot be used as GROUP BY columns.
* This function cannot use DISTINCT and ALL.
* This function cannot specify the OVER keyword to be used as a window function.

***Example*** for Heap tables

```sql
-- 1. Aggregate two GEOMETRYs using the regular function
SELECT ST_ASTEXT(ST_COLLECT(ST_GEOMFROMTEXT('POINT(1 2)'), ST_GEOMFROMTEXT('POINT(3 4)')), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
MULTIPOINT (1 2, 3 4)

-- 2. Create table
CREATE TABLE geom(id INT, col_geom GEOMETRY);
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('POINT(1 2)'));
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('LINESTRING(3 4, 5 2)'));

-- 3. Aggregate GEOMETRYs in the table using the aggregate function
SELECT ST_ASTEXT(ST_COLLECT(col_geom), 0) res FROM geom;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION (POINT (1 2), LINESTRING (3 4, 5 2))
```
