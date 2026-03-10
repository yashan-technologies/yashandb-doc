```ebnf+diagram
st_clipbybox2d::= ST_CLIPBYBOX2D "(" geometry1 "," geometry2 ")"
```

The ST_CLIPBYBOX2D function returns the geometry resulting from the rectangular clipping box calculated from geometry2 applied to geometry1. The return value is of type ST_GEOMETRY.

> **Note**: 
>
> The coordinates of the rectangular clipping box calculated from geometry2 are (Xmin, Ymin, Xmax, Ymax), where Xmin is the minimum X value of geometry2, Ymin is the minimum Y value of geometry2, Xmax is the maximum X value of geometry2, and Ymax is the maximum Y value of geometry2.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

This function adheres to the following rules:

* When the clipped shape is EMPTY, the function returns EMPTY of the clipped shape's corresponding type.
* When the clipped shape is of type POINT or LINESTRING, the function returns GEOMETRYCOLLECTION EMPTY.
* When the clipped shape is contained within the clipping box, the function returns the clipped shape.
* When the clipped shape does not intersect the clipping box at all, the function returns EMPTY of the clipped shape's type.
* When the clipping box is EMPTY, NaN, POINT type, vertical line, and horizontal line, the function returns NULL.
* When there is a NULL in the input parameters, the function returns NULL.
* Only 2D results are calculated; if there is a Z coordinate in the input parameters, the function will directly ignore the Z coordinate for calculation.

***Example*** for Heap tables

```sql
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0 2, 2 2, 2 0, 0 2))  

-- When the clipped shape is EMPTY, return corresponding type EMPTY
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON EMPTY'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;
          
RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY            

-- When the clipped shape is of type POINT, return GEOMETRYCOLLECTION EMPTY
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POINT(1 2)'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY  

-- When the clipped shape is contained within the clipping box, return the clipped shape
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((0 0,0 1, 1 1, 1 0, 0 0))'), ST_GEOMFROMTEXT('MULTIPOINT(0 0, 0 2, 2 2, 2 0)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((0 0, 0 1, 1 1, 1 0, 0 0)) 

-- When the clipped shape does not intersect the clipping box at all, return EMPTY of the clipped shape's type
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((0 0,0 1, 1 1, 1 0, 0 0))'), ST_GEOMFROMTEXT('MULTIPOINT(6 6, 6 7, 7 7, 7 6)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON EMPTY 

-- When the clipping box is EMPTY, return NULL
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), ST_GEOMFROMTEXT('MULTIPOINT EMPTY')), 0) res FROM DUAL;         
          
RES                                                              
---------------------------------------------------------------- 
                                                                
-- When there is a NULL in the parameters, return NULL
SELECT ST_ASTEXT(ST_CLIPBYBOX2D(ST_GEOMFROMTEXT('POLYGON((2 0, 3 0, 3 2, 2 3, 0 2, 2 0))'), NULL), 0) res FROM DUAL;  

RES                                                              
---------------------------------------------------------------- 
                                                                
```
