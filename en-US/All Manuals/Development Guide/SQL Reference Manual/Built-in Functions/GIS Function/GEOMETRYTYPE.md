```ebnf+diagram
geometrytype::= GEOMETRYTYPE "(" geometry ")"
```

The GEOMETRYTYPE function is used to print the type of the input geometry.

This function returns the data type of the geometry based on the input, with the return value being of type VARCHAR and entirely in uppercase.

Compared to the [ST_GEOMETRYTYPE](ST_GEOMETRYTYPE) function, the return value of the GEOMETRYTYPE function does not have the ST_ prefix and is entirely in uppercase.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be a valid ST_GEOMETRY type.

When the input parameter contains NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_GeomFromText function returns an ST_GEOMETRY data given the WKT and SRID
SELECT GEOMETRYTYPE(ST_GeomFromText('POINT(-58.2687 29.149)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT   
        
SELECT GEOMETRYTYPE(ST_GeomFromText('LINESTRING(1 6,3 2,9 7)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING  

-- Parameter contains NULL
SELECT GEOMETRYTYPE(ST_GeomFromText(null,1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
                                                                      
```
