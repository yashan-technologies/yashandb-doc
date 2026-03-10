```ebnf+diagram
st_point::= ST_POINT "(" x "," y [ "," srid ] ")"
```

The ST_POINT function returns a POINT data type corresponding to the input x, y, and optional srid.

**x, y**

Represents the coordinates, the parameter type is numeric, following these rules:

- Supports CHAR and VARCHAR types that can be converted to DOUBLE (conversion failure returns Invalid Number error), using other data types will return an error.
- This function only supports two-dimensional coordinates; using coordinates of other dimensions will return an error.

**srid**

The data type of srid is INT, representing the spatial reference system in the output result, following these rules:

*   Supports types that can be implicitly converted to INT; if the input is a decimal, it will be rounded.
*   This parameter can be omitted; if omitted, the default value is 0.
*   If the input is a negative number, the default srid will be output.

When any input parameter is NULL, the function returns NULL.

To obtain three-dimensional POINT data, please use the [ST_POINTZ](ST_POINTZ) function.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_Point(1, 2), 0) res FROM DUAL;
RES                                        
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_Point(1, 2, 4326), 0) res FROM DUAL;

RES                                         
---------------------------------------------------------------- 
POINT (1 2)

-- When the SRID is negative, the default SRID is output
SELECT ST_SRID(ST_Point(1, 2, -8)) res FROM DUAL;

                 RES
-------------------- 
                   0

SELECT ST_SRID(ST_Point(1, 2)) res FROM DUAL;

                 RES
-------------------- 
                   0

SELECT ST_SRID(ST_Point(1, 2, 4326)) res FROM DUAL;

                 RES 
-------------------- 
                4326
```
