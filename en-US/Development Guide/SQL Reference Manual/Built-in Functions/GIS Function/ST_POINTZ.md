```ebnf
st_pointz = ST_POINTZ "(" x "," y "," z [ "," srid ] ")".
```

The ST_POINTZ function returns a POINT data type corresponding to the coordinates and SRID based on the input values x, y, z, and the optional srid.

**x, y, z**

These represent coordinates, with a parameter type of numeric, and are subject to the following rules:

- Supports CHAR and VARCHAR types that can be converted to DOUBLE (conversion failure returns Invalid Number error). Using other data types will return an error.
- This function only supports three-dimensional coordinates. Using coordinates of different dimensions will return an error.

**srid**

The data type of srid is INT, which represents the spatial reference system in the output results, following these rules:

*   Supports types that can be implicitly converted to INT. For decimal inputs, rounding will be applied.
*   This parameter can be omitted; when omitted, the default value is 0.
*   If a negative number is provided, the default srid will be used.

If any input parameter is NULL, the function will return NULL.

To obtain POINT data with two-dimensional coordinates, please use the [ST_POINT](ST_POINT) function.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_PointZ(1, 2, 3), 0) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
POINT Z (1 2 3)        

SELECT ST_AsText(ST_PointZ(1, 2, 3, 4326), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT Z (1 2 3)  

--When SRID is a negative number, the default SRID will be used
SELECT ST_SRID(ST_PointZ(1, 2, 3, -8)) res FROM DUAL;

                 RES
-------------------- 
                   0                                            

SELECT ST_SRID(ST_PointZ(1, 2, 3)) res FROM DUAL;

                 RES 
-------------------- 
                   0

SELECT ST_SRID(ST_PointZ(1, 2, 3, 4326)) res FROM DUAL;

                 RES 
-------------------- 
                4326
```
