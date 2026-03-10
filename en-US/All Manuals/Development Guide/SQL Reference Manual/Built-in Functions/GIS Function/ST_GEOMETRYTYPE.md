```ebnf+diagram
st_geometrytype::= ST_GEOMETRYTYPE "(" geometry ")"
```

The ST_GEOMETRYTYPE function is used to print the type of the input geometry.

This function returns the data type of the input geometry, with the return value being of VARCHAR type.

Compared to the [GEOMETRYTYPE](GEOMETRYTYPE) function, the return value of the ST_GEOMETRYTYPE function has the ST_ prefix and is not fully uppercase.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be a valid ST_GEOMETRY type data.

When the input parameter is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- The ST_GEOMFROMTEXT function returns an ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_GEOMETRYTYPE(ST_GeomFromText('POINT(-58.2687 29.149)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
ST_Point 

SELECT ST_GEOMETRYTYPE(ST_GeomFromText('LINESTRING(1 6,3 2,9 7)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
ST_LineString   

-- Parameter contains NULL
SELECT ST_GEOMETRYTYPE(ST_GeomFromText(null,1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
                                                                
```
