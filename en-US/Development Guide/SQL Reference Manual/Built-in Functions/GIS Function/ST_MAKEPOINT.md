```ebnf
st_makepoint = ST_MAKEPOINT "(" x "," y [ "," z [ "," m ] ] ")".
```

The ST_MAKEPOINT function returns a POINT data type corresponding to the given x, y coordinates, with optional z and m coordinates.

**x, y, z, m**

These represent coordinates, and the parameter types are numeric. CHAR and VARCHAR types that can be converted to DOUBLE are supported (conversion failure results in an Invalid Number error). Use of other data types will result in an error.

If any input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_MakePoint(1, 2), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_MakePoint(1, 2, 3), 0) res FROM DUAL;

RES                                        
---------------------------------------------------------------- 
POINT Z (1 2 3)                                                 

SELECT ST_AsText(ST_MakePoint(1, 2, 3, 4), 0) res FROM DUAL;

RES                                        
---------------------------------------------------------------- 
POINT Z (1 2 3)
```
