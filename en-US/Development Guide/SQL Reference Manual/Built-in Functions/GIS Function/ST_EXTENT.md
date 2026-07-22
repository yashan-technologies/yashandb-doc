```ebnf
st_extent = ST_EXTENT "(" geomFiled ")".
```

The functionality of the ST_EXTENT function is to return a two-dimensional bounding box that encloses a set of geometries. It is an aggregate function, and the return type is BOX2D.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

This function adheres to the following rules:

* If the input set of geometries is all NULL or all EMPTY, this function returns NULL.
* This function only computes 2D results. If there are Z coordinates, they will be ignored in calculations.
* Geometry cannot be used as a GROUP BY column.
* This function cannot use DISTINCT and ALL.
* This function cannot specify the OVER keyword to be used as a window function.

***Example*** for Heap tables

```sql
-- 1. Create table
DROP TABLE IF EXISTS geom;
CREATE TABLE geom(id INT, col_geom GEOMETRY);
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('POINT(1 2)'));
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('LINESTRING(3 4, 5 2)'));

-- 2. Create BOX2D output function 
CREATE OR REPLACE FUNCTION BOX2D_OUT(box BOX2D) RETURN VARCHAR AS
    res VARCHAR(100);
BEGIN
    res := 'BOX(' || box.xmin || ' ' || box.xmax || ',' || box.ymin || ' ' || box.ymax || ')';
RETURN res;
END;
/
    
-- 3. Aggregate geometry in the table using aggregate function
SELECT BOX2D_OUT(ST_EXTENT(col_geom)) res FROM geom;

RES
---------------------------------------------------------------- 
BOX(1.0E+000 5.0E+000,2.0E+000 4.0E+000)
```
